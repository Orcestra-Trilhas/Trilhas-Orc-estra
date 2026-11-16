# Unidade IX: Spring Security e Autenticação

---

## 1. Visão Geral

Nesta unidade final você protege a API: **autenticação** de usuários, senhas com **BCrypt**, **tokens JWT** e controle de acesso a rotas, incluindo a regra mais comum em sistemas reais: *"o usuário só mexe nos próprios recursos"*.

Ao final, sua API identificará quem faz cada requisição e decidirá o que essa pessoa pode ou não acessar.

!!! note "Pré-requisitos"
    Usa a API completa da Unidade VIII (DTOs, `@RestControllerAdvice`), as entidades da Unidade VII, variáveis de ambiente e `@Value` (Unidade VI) e os status `401`/`403` (Unidade V).

!!! warning "Versão"
    O material usa **Spring Security 6** (Spring Boot 3.x). Tutoriais antigos usam `WebSecurityConfigurerAdapter` e `antMatchers`, que **foram removidos**. Se o código de um vídeo ou artigo usa essas classes, ele está desatualizado para este projeto.

---

## 2. Conceitos

### 9.1 Autenticação vs. Autorização

- **Autenticação** responde "quem é você?": verificar a identidade (login e senha).
- **Autorização** responde "o que você pode fazer?": verificar se o usuário autenticado tem permissão.

Mapeamento nos status HTTP: não autenticado → **`401 Unauthorized`**; autenticado sem permissão → **`403 Forbidden`**.

### 9.2 Filter Chain (Cadeia de Filtros)

O Spring Security intercepta **toda requisição** antes do Controller, passando-a por uma sequência de filtros (`SecurityFilterChain`). Se a requisição passar por toda a cadeia, chega ao Controller; senão, o Security responde com erro (`401`/`403`) antes de qualquer código de negócio. Você configura a cadeia declarando um **Bean** `SecurityFilterChain`.

### 9.3 `UserDetails` e `UserDetailsService`

- **`UserDetails`**: representa o usuário do ponto de vista do Security (login, senha, permissões, conta ativa...).
- **`UserDetailsService`**: tem um método, `loadUserByUsername`, que você implementa para dizer **como buscar** o usuário no banco.

```java
@Service
public class UsuarioDetailsService implements UserDetailsService {

    private final UsuarioRepository repository;

    public UsuarioDetailsService(UsuarioRepository repository) {
        this.repository = repository;
    }

    @Override
    public UserDetails loadUserByUsername(String email) throws UsernameNotFoundException {
        Usuario u = repository.findByEmail(email)
                .orElseThrow(() -> new UsernameNotFoundException("Usuário não encontrado"));

        return org.springframework.security.core.userdetails.User
                .withUsername(u.getEmail())
                .password(u.getSenhaHash())
                .roles(u.getPapel().name())     // USER ou ADMIN -> vira ROLE_USER / ROLE_ADMIN
                .build();
    }
}
```

Para isso, a entidade `Usuario` ganha `senhaHash` (**nunca** `senha`) e um enum `Papel { USER, ADMIN }` (gravado com `EnumType.STRING`), e o `UsuarioRepository` ganha `Optional<Usuario> findByEmail(String email)`.

### 9.4 Senhas: `PasswordEncoder` e BCrypt

Senhas **nunca** são guardadas em texto puro. O **BCrypt** é um algoritmo de **hash com salt automático**: duas senhas iguais geram hashes diferentes, e o processo é intencionalmente lento, o que dificulta força bruta. Hash é **unidirecional**: não se "descriptografa"; compara-se.

```java
passwordEncoder.encode("minhaSenha123");                  // gera o hash para salvar
passwordEncoder.matches("minhaSenha123", hashDoBanco);    // compara
```

Ao cadastrar um usuário, o Service chama `encode` e salva só o hash. O `Response` DTO do usuário **nunca** inclui senha nem hash.

### 9.5 Autenticação Stateless e JWT

APIs REST são **stateless** (Unidade V): o servidor não guarda sessão. O cliente prova quem é a cada requisição enviando um **token**. O formato mais comum é o **JWT (JSON Web Token)**: três partes em Base64 separadas por ponto (`header.payload.assinatura`).

Fluxo:
1. `POST /auth/login` com e-mail e senha. O servidor valida e devolve um token assinado, com validade.
2. Nas requisições seguintes, o cliente envia `Authorization: Bearer <token>`.
3. Um filtro valida a assinatura e a validade e identifica o usuário, **sem consultar o banco** a cada requisição.

!!! danger "O payload do JWT é assinado, não criptografado"
    Qualquer pessoa decodifica o payload em Base64 (teste em [jwt.io](https://jwt.io)). A assinatura só garante que **não foi alterado**. Nunca coloque senha, CPF ou dados sensíveis no token: use só identificadores (id, e-mail, papel).

**Segredo e expiração:**
- O token é assinado com um **segredo** que só o servidor conhece. Se vazar, qualquer um forja tokens. **Nunca** commite o segredo no Git: leia de variável de ambiente (`${JWT_SECRET}`) e adicione arquivos locais de segredos ao `.gitignore`. Use um segredo longo e aleatório.
- Tokens têm **expiração curta** (ex.: 1 a 2 horas). Depois de expirado, o cliente faz login de novo. *Refresh tokens* (um segundo token, de vida longa, que pede novos tokens de acesso) existem para melhorar a experiência, mas estão fora do escopo da trilha.
- Um JWT válido não pode ser "cancelado" antes de expirar sem estruturas extras (lista de bloqueio). É a contrapartida do modelo sem estado.

**Sessão vs. token:** aplicações web tradicionais guardam a sessão no servidor (cookie com ID de sessão). A trilha usa **apenas JWT**.

---

## 3. Implementação

Dependências: `spring-boot-starter-security`, `com.auth0:java-jwt` (use a versão estável mais recente do Maven Central) e, para testes, `spring-security-test`.

Propriedades (`application.properties`):

```properties
api.security.token.secret=${JWT_SECRET}
api.security.token.expiracao-horas=2
```

Defina `JWT_SECRET` como variável de ambiente (na IDE: *Run Configuration → Environment variables*). Sem ela, a aplicação falha ao subir, que é o comportamento desejado.

### 9.6 Gerando e Validando o Token

```java
@Service
public class TokenService {

    private static final String EMISSOR = "gerenciador-tarefas";

    @Value("${api.security.token.secret}")
    private String segredo;

    @Value("${api.security.token.expiracao-horas}")
    private long horas;

    public String gerar(Usuario usuario) {
        return JWT.create()
                .withIssuer(EMISSOR)
                .withSubject(usuario.getEmail())
                .withClaim("id", usuario.getId())
                .withClaim("papel", usuario.getPapel().name())
                .withExpiresAt(Instant.now().plus(Duration.ofHours(horas)))
                .sign(Algorithm.HMAC256(segredo));
    }

    // Lança JWTVerificationException se a assinatura for inválida ou o token estiver expirado
    public DecodedJWT validar(String token) {
        return JWT.require(Algorithm.HMAC256(segredo))
                .withIssuer(EMISSOR)
                .build()
                .verify(token);
    }
}
```

### 9.7 O Principal Autenticado

Para a regra de dono do recurso, você precisa do **id** de quem está logado. Crie um tipo simples para ser o "principal":

```java
public record UsuarioAutenticado(Long id, String email) { }
```

### 9.8 O Filtro JWT

Um filtro que roda **uma vez por requisição**, lê o header, valida o token e registra a autenticação no `SecurityContext`:

```java
@Component
public class JwtAuthFilter extends OncePerRequestFilter {

    private final TokenService tokenService;

    public JwtAuthFilter(TokenService tokenService) {
        this.tokenService = tokenService;
    }

    @Override
    protected void doFilterInternal(HttpServletRequest request, HttpServletResponse response,
                                    FilterChain chain) throws ServletException, IOException {

        String header = request.getHeader("Authorization");

        if (header != null && header.startsWith("Bearer ")) {
            try {
                DecodedJWT jwt = tokenService.validar(header.substring(7));

                var principal = new UsuarioAutenticado(jwt.getClaim("id").asLong(), jwt.getSubject());
                var autoridades = List.of(new SimpleGrantedAuthority("ROLE_" + jwt.getClaim("papel").asString()));

                var autenticacao = new UsernamePasswordAuthenticationToken(principal, null, autoridades);
                SecurityContextHolder.getContext().setAuthentication(autenticacao);
            } catch (JWTVerificationException e) {
                // token inválido ou expirado: segue sem autenticação, e o Security responde 401
            }
        }

        chain.doFilter(request, response);
    }
}
```

### 9.9 Configurando a Cadeia de Filtros

```java
@Configuration
@EnableWebSecurity
@EnableMethodSecurity            // habilita @PreAuthorize
public class SecurityConfig {

    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http, JwtAuthFilter jwtFilter) throws Exception {
        return http
                .csrf(csrf -> csrf.disable())     // seguro aqui: sem cookies de sessão, o token vai no header
                .cors(Customizer.withDefaults())  // usa o CORS configurado na Unidade VIII
                .sessionManagement(s -> s.sessionCreationPolicy(SessionCreationPolicy.STATELESS))
                .authorizeHttpRequests(auth -> auth
                        .requestMatchers("/auth/**").permitAll()
                        .requestMatchers("/swagger-ui/**", "/v3/api-docs/**").permitAll()
                        .requestMatchers(HttpMethod.DELETE, "/usuarios/**").hasRole("ADMIN")
                        .anyRequest().authenticated())
                .exceptionHandling(e -> e
                        .authenticationEntryPoint(new HttpStatusEntryPoint(HttpStatus.UNAUTHORIZED)))
                .addFilterBefore(jwtFilter, UsernamePasswordAuthenticationFilter.class)
                .build();
    }

    @Bean
    public PasswordEncoder passwordEncoder() {
        return new BCryptPasswordEncoder();
    }

    @Bean
    public AuthenticationManager authenticationManager(AuthenticationConfiguration config) throws Exception {
        return config.getAuthenticationManager();
    }
}
```

Pontos que costumam dar dor de cabeça:
- **Sem o `authenticationEntryPoint` acima, requisições sem token podem receber `403` em vez de `401`.** Configure explicitamente.
- A **ordem** em `authorizeHttpRequests` importa: a primeira regra que casa vale. `anyRequest()` fica por último.
- No Spring Security 6 use `requestMatchers(...)` (não `antMatchers`).
- O console do H2 (`/h2-console`) é bloqueado pelo Security. Libere-o **apenas no profile de desenvolvimento** (`permitAll` para `/h2-console/**` e `headers.frameOptions(f -> f.sameOrigin())`), nunca em produção.

### 9.10 Login e Cadastro

```java
@RestController
@RequestMapping("/auth")
public class AuthController {

    private final AuthenticationManager authenticationManager;
    private final UsuarioRepository usuarioRepository;
    private final TokenService tokenService;
    // + UsuarioService para o cadastro (usa passwordEncoder.encode)

    @PostMapping("/login")
    public ResponseEntity<TokenResponse> login(@Valid @RequestBody LoginRequest request) {
        authenticationManager.authenticate(
                new UsernamePasswordAuthenticationToken(request.email(), request.senha()));
        // se as credenciais forem inválidas, uma exceção é lançada (veja o handler abaixo)

        Usuario usuario = usuarioRepository.findByEmail(request.email()).orElseThrow();
        return ResponseEntity.ok(new TokenResponse(tokenService.gerar(usuario)));
    }

    @PostMapping("/registro")
    public ResponseEntity<UsuarioResponse> registrar(@Valid @RequestBody RegistroRequest request) {
        // valida e-mail único (409), faz encode da senha, papel USER por padrão, devolve 201
    }
}
```

Os DTOs são Records: `LoginRequest(String email, String senha)`, `TokenResponse(String token)`. **Atenção:** o cadastro público deve criar sempre o papel `USER`; permitir que o cliente envie `"papel": "ADMIN"` é uma falha grave de segurança.

Para que as exceções de segurança virem respostas corretas no seu `@RestControllerAdvice` (Unidade VIII), adicione:

```java
@ExceptionHandler(BadCredentialsException.class)
public ProblemDetail tratarCredenciais(BadCredentialsException ex) {
    ProblemDetail pd = ProblemDetail.forStatusAndDetail(HttpStatus.UNAUTHORIZED, "E-mail ou senha inválidos");
    pd.setTitle("Não autenticado");
    return pd;
}

@ExceptionHandler(AccessDeniedException.class)
public ProblemDetail tratarAcessoNegado(AccessDeniedException ex) {
    ProblemDetail pd = ProblemDetail.forStatusAndDetail(HttpStatus.FORBIDDEN, "Você não tem permissão para este recurso");
    pd.setTitle("Acesso negado");
    return pd;
}
```

!!! danger "Sem o handler de `AccessDeniedException`, o 403 vira 500"
    O handler genérico `Exception` da Unidade VIII captura a `AccessDeniedException` lançada dentro do Controller ou do Service e devolve `500`. O handler específico acima corrige isso. Use mensagens de login **genéricas** ("e-mail ou senha inválidos"), sem revelar qual dos dois estava errado.

---

## 4. Controle de Acesso

### 9.11 Por Perfil (Role)

Duas formas, que podem coexistir:

```java
// 1) Na SecurityFilterChain (por rota): .requestMatchers(HttpMethod.DELETE, "/usuarios/**").hasRole("ADMIN")

// 2) No método (granular):
@PreAuthorize("hasRole('ADMIN')")
@GetMapping("/relatorios")
public List<RelatorioResponse> relatorios() { ... }
```

`hasRole('ADMIN')` procura a autoridade `ROLE_ADMIN`.

### 9.12 Por Dono do Recurso

`hasRole` não resolve "só o próprio usuário edita e exclui suas tarefas". Para isso você precisa do **principal autenticado**. O Controller o recebe com `@AuthenticationPrincipal` e o repassa ao Service, que decide:

```java
@DeleteMapping("/{id}")
public ResponseEntity<Void> remover(@PathVariable Long id,
                                    @AuthenticationPrincipal UsuarioAutenticado logado) {
    service.remover(id, logado);
    return ResponseEntity.noContent().build();
}

// No Service
@Transactional
public void remover(Long tarefaId, UsuarioAutenticado logado) {
    Tarefa tarefa = repository.findById(tarefaId)
            .orElseThrow(() -> new TarefaNaoEncontradaException(tarefaId));

    boolean dono = tarefa.getUsuario().getId().equals(logado.id());
    boolean admin = SecurityContextHolder.getContext().getAuthentication().getAuthorities().stream()
            .anyMatch(a -> a.getAuthority().equals("ROLE_ADMIN"));

    if (!dono && !admin) {
        throw new AccessDeniedException("A tarefa pertence a outro usuário");   // vira 403 pelo handler
    }
    repository.delete(tarefa);
}
```

Para a criação, **ignore o `usuarioId` do corpo** e use o id do principal: senão, um usuário poderia criar tarefas em nome de outro. Para as listagens, filtre pelo usuário logado (`findByUsuarioId(logado.id())`).

Uma decisão de design a registrar no README: quando a tarefa existe mas pertence a outro, responder `403` revela que o id existe; responder `404` não revela. Ambas são defensáveis; escolha uma e seja consistente.

---

## 5. Testando

Com `spring-security-test`, simule usuários nos testes do Controller:

```java
@Test
@WithMockUser(roles = "USER")
void usuarioComumNaoPodeAcessarRotaDeAdmin() throws Exception {
    mockMvc.perform(get("/relatorios")).andExpect(status().isForbidden());
}

@Test
void semTokenDeveRetornar401() throws Exception {
    mockMvc.perform(get("/tarefas")).andExpect(status().isUnauthorized());
}
```

E no Postman: faça `POST /auth/login`, copie o token e envie em `Authorization → Bearer Token`. Teste o **fluxo negativo**: sem token (401), token adulterado (401), token de outro usuário em recurso alheio (403).

---

## 6. Conteúdo em Vídeo

- 🎥 [Vídeo: Curso Spring Boot, Dominando o Spring Security](https://www.youtube.com/watch?v=KYa4xQaQ7SU&t=813s&pp=ygUJc291emEgZGV2)

!!! warning "Confira a versão do vídeo antes de seguir o código"
    Verifique se o vídeo usa `SecurityFilterChain` como `@Bean` (Spring Security 6). Se aparecer `WebSecurityConfigurerAdapter`, `antMatchers` ou `javax.*`, o código está desatualizado; use os trechos deste material.

- 📚 [Documentação oficial do Spring Security](https://docs.spring.io/spring-security/reference/)

---

## 7. Checklist de Conclusão

- [ ] Diferencio autenticação de autorização e `401` de `403`.
- [ ] Meu cadastro grava apenas o **hash** BCrypt e o DTO de resposta nunca devolve a senha.
- [ ] O login devolve um JWT com expiração; o segredo vem de variável de ambiente e **não** está no Git.
- [ ] Explico por que o payload do JWT não pode conter dados sensíveis.
- [ ] Rotas públicas (`/auth/**`, Swagger) funcionam sem token; todas as demais exigem token.
- [ ] Um usuário **não** edita nem exclui tarefas de outro (recebe `403`); um `ADMIN` consegue.
- [ ] O cadastro público não aceita escolher o papel `ADMIN`.
- [ ] Sem token recebo `401` (não `403`), com corpo no formato `ProblemDetail`.
- [ ] Escrevi testes para o fluxo negativo (sem token, papel insuficiente).

---

## 8. Projeto Final da Trilha

Implemente autenticação e segurança na API do **Gerenciador de Tarefas**:

- 🌐 [Instruções do Projeto Final: Spring Security ➔](desafio.md#projeto-final)

---

## 9. Aprofundamento de Carreira

Depois do projeto final, os próximos passos para trabalhar em projetos reais são: **banco real (PostgreSQL) e Docker**, **migrations (Flyway)**, **testes de integração** e **CI/CD**.

- 🌐 [Roadmap Completo de Carreira Back-end (Roadmap.sh)](https://roadmap.sh/backend)
