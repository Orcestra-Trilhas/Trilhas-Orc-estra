# Unidade VIII: Spring Web

---

## 1. Visão Geral

Enquanto o Spring Data JPA cuida da persistência, o **Spring Web** (`spring-boot-starter-web`) expõe a aplicação como uma API HTTP: recebe requisições, extrai dados, chama o Service e devolve respostas.

Aqui você aprende como uma requisição chega ao seu código, como mapear rotas, **por que não expor entidades e usar DTOs**, como controlar status codes, validar dados, **padronizar erros em um formato único**, documentar a API com Swagger e testá-la.

!!! note "Pré-requisitos"
    Usa exceções customizadas (Unidade II), Records e `Optional` (Unidade III), verbos e status codes (Unidade V), a arquitetura em camadas (Unidade VI) e as entidades JPA (Unidade VII).

---

## 2. Fundamentos do Spring Web

### 8.1 Como uma Requisição Chega ao Controller

- **`DispatcherServlet`**: o "front controller" do Spring MVC. Todo request HTTP passa primeiro por ele. Ele consulta o **Handler Mapping** para descobrir qual método do Controller trata aquela URL + verbo e delega a chamada.
- **Handler Mapping**: casa URL + verbo HTTP com o método certo, a partir de `@RequestMapping`/`@GetMapping`/etc.
- Quando o método retorna, o `DispatcherServlet` entrega o valor a um **`HttpMessageConverter`** (por padrão o Jackson, para JSON), que serializa o objeto Java em JSON.

Fluxo: `Requisição → [Filtros de Security] → DispatcherServlet → Handler Mapping → Controller → HttpMessageConverter → Resposta`.

### 8.2 Criando um Controller REST

- **`@RestController`**: combina `@Controller` (Bean que recebe requisições) com `@ResponseBody` (o retorno vai direto para o corpo, como JSON).
- **`@RequestMapping("/tarefas")`**: prefixo de rota para a classe.

```java
@RestController
@RequestMapping("/tarefas")
public class TarefaController {

    private final TarefaService service;

    public TarefaController(TarefaService service) {
        this.service = service;
    }
}
```

### 8.3 Mapeando Rotas por Verbo HTTP

```java
@GetMapping                  // GET    /tarefas
@GetMapping("/{id}")         // GET    /tarefas/{id}
@PostMapping                 // POST   /tarefas
@PutMapping("/{id}")         // PUT    /tarefas/{id}      (substitui)
@PatchMapping("/{id}")       // PATCH  /tarefas/{id}      (parcial)
@DeleteMapping("/{id}")      // DELETE /tarefas/{id}
```

### 8.4 Extraindo Dados da Requisição

- **`@PathVariable`**: valor da URL. `/tarefas/{id}` → `@PathVariable Long id`.
- **`@RequestParam`**: query parameter. `/tarefas?concluida=true` → `@RequestParam Boolean concluida`. Opcional com `required = false` ou `defaultValue`.
- **`@RequestBody`**: converte o JSON do corpo em um objeto Java (via `HttpMessageConverter`). Para `POST`/`PUT`/`PATCH`.
- **`@RequestHeader`**: lê um cabeçalho. `@RequestHeader("Accept-Language") String idioma`.

---

## 3. DTOs: Não Exponha Entidades

Receber `@RequestBody Tarefa` ou devolver `ResponseEntity<Tarefa>` funciona em um exemplo de dois minutos, mas é um **anti-padrão** em qualquer projeto real:

1. **Acopla a API ao banco:** renomear uma coluna muda o contrato que o front-end consome.
2. **Recursão infinita no JSON:** `Usuario` tem `tarefas` e `Tarefa` tem `usuario`; o Jackson serializa um dentro do outro até estourar a pilha (`StackOverflowError`).
3. **Vazamento de dados:** a entidade `Usuario` terá o hash da senha (Unidade IX); serializá-la o exporia.
4. **Mass assignment:** o cliente poderia enviar `"id"` ou `"concluida"` no JSON e sobrescrever campos que ele não deveria controlar.
5. **`LazyInitializationException`:** serializar a entidade fora da transação tenta carregar relações `LAZY` (Unidade VII).

A solução é um **DTO** (*Data Transfer Object*): uma classe própria para a entrada e outra para a saída. Com Records (Unidade III) ficam curtos:

```java
// entrada: só o que o cliente pode mandar
public record TarefaRequest(
        @NotBlank(message = "O título é obrigatório")
        @Size(max = 150, message = "O título deve ter no máximo 150 caracteres")
        String titulo,

        String descricao,

        @NotNull(message = "O usuário é obrigatório")
        Long usuarioId,

        @FutureOrPresent(message = "O prazo não pode estar no passado")
        LocalDate prazo,

        Prioridade prioridade
) { }

// saída: só o que a API decide expor
public record TarefaResponse(
        Long id, String titulo, String descricao, boolean concluida,
        Long usuarioId, LocalDate prazo, Prioridade prioridade
) {
    public static TarefaResponse de(Tarefa t) {
        return new TarefaResponse(t.getId(), t.getTitulo(), t.getDescricao(), t.isConcluida(),
                t.getUsuario().getId(),
                t instanceof TarefaComPrazo cp ? cp.getPrazo() : null,
                t instanceof TarefaComPrazo cp ? cp.getPrioridade() : null);
    }
}
```

O **Service** recebe o `TarefaRequest`, decide se cria uma `Tarefa` simples ou uma `TarefaComPrazo` (conforme `prazo` e `prioridade` vieram preenchidos), salva e devolve o `TarefaResponse`. Assim, a entidade **nunca sai do Service**. Isso também resolve o `usuarioId` do payload: o Service busca o `Usuario` e o associa.

Para listas, mapeie com stream: `tarefas.stream().map(TarefaResponse::de).toList()`.

---

## 4. Formatando a Resposta: `ResponseEntity`

Retornar um objeto direto sempre devolve `200 OK`. Para controlar **status code** e **headers**, use `ResponseEntity<T>`:

```java
@GetMapping("/{id}")
public TarefaResponse buscar(@PathVariable Long id) {
    return service.buscarPorId(id);   // o Service lança TarefaNaoEncontradaException se não existir
}

@PostMapping
public ResponseEntity<TarefaResponse> criar(@Valid @RequestBody TarefaRequest request) {
    TarefaResponse criada = service.criar(request);

    URI location = ServletUriComponentsBuilder.fromCurrentRequest()
            .path("/{id}")
            .buildAndExpand(criada.id())
            .toUri();

    return ResponseEntity.created(location).body(criada);       // 201 + header Location
}

@DeleteMapping("/{id}")
public ResponseEntity<Void> remover(@PathVariable Long id) {
    service.remover(id);
    return ResponseEntity.noContent().build();                   // 204
}
```

Convenções (Unidade V): `POST` bem-sucedido devolve **201** com o header `Location`; `DELETE` devolve **204** sem corpo; `GET`/`PUT`/`PATCH` devolvem **200**. Repare que o "404 quando não existe" **não** está no Controller: o Service lança a exceção e o tratamento centralizado (seção 6) a converte. Isso mantém o Controller fino.

**Paginação em listagens:** receba um `Pageable` e devolva uma `Page`:

```java
@GetMapping
public Page<TarefaResponse> listar(
        @RequestParam(required = false) Boolean concluida,
        @PageableDefault(size = 10, sort = "titulo") Pageable pageable) {
    return service.listar(concluida, pageable);
}
// GET /tarefas?concluida=false&page=0&size=5&sort=titulo,desc
```

---

## 5. Validação de Dados (`@Valid`)

A validação exige a dependência `spring-boot-starter-validation` (ela **não** vem no starter web). Combine **Bean Validation** nas classes (`@NotBlank`, `@NotNull`, `@Size`, `@Email`, `@Past`, `@Future`...) com `@Valid` no parâmetro do Controller (exemplos no `TarefaRequest` acima).

Se a validação falhar, o Spring lança `MethodArgumentNotValidException` **antes** do seu código de negócio executar. A resposta de erro fica por conta do tratamento centralizado.

!!! tip "Validação de formato vs. regra de negócio"
    Bean Validation cobre **formato** (campo vazio, tamanho, e-mail válido). Regras que dependem do banco ou do contexto (e-mail já cadastrado, usuário inexistente) ficam no **Service**, que lança exceções próprias.

---

## 6. Tratamento Centralizado de Erros

Em vez de `try/catch` repetido em cada Controller, centralize:

- **`@ExceptionHandler`**: método que captura um tipo de exceção e define a resposta.
- **`@RestControllerAdvice`** (`@ControllerAdvice` + `@ResponseBody`): aplica os handlers a **todos** os Controllers, devolvendo JSON.

Para que o formato de erro seja **realmente padronizado**, use **um corpo único** para todos os erros. O Spring 6 traz o `ProblemDetail`, que implementa o padrão **RFC 9457 (Problem Details for HTTP APIs)**, com os campos `type`, `title`, `status`, `detail` e `instance`, e aceita campos extras:

```java
@RestControllerAdvice
public class GlobalExceptionHandler extends ResponseEntityExceptionHandler {

    private static final Logger log = LoggerFactory.getLogger(GlobalExceptionHandler.class);

    @ExceptionHandler(RecursoNaoEncontradoException.class)
    public ProblemDetail tratarNaoEncontrado(RecursoNaoEncontradoException ex) {
        ProblemDetail pd = ProblemDetail.forStatusAndDetail(HttpStatus.NOT_FOUND, ex.getMessage());
        pd.setTitle("Recurso não encontrado");
        return pd;
    }

    @ExceptionHandler(ConflitoException.class)       // ex.: e-mail já cadastrado
    public ProblemDetail tratarConflito(ConflitoException ex) {
        ProblemDetail pd = ProblemDetail.forStatusAndDetail(HttpStatus.CONFLICT, ex.getMessage());
        pd.setTitle("Conflito");
        return pd;
    }

    // Erros de validação do @Valid: devolve o mesmo formato, com a lista de campos inválidos
    @Override
    protected ResponseEntity<Object> handleMethodArgumentNotValid(
            MethodArgumentNotValidException ex, HttpHeaders headers,
            HttpStatusCode status, WebRequest request) {

        Map<String, String> erros = new LinkedHashMap<>();
        ex.getBindingResult().getFieldErrors()
                .forEach(e -> erros.put(e.getField(), e.getDefaultMessage()));

        ProblemDetail pd = ProblemDetail.forStatusAndDetail(HttpStatus.BAD_REQUEST, "Dados inválidos");
        pd.setTitle("Erro de validação");
        pd.setProperty("erros", erros);
        return ResponseEntity.badRequest().body(pd);
    }

    // Último recurso: nunca vaza stack trace para o cliente
    @ExceptionHandler(Exception.class)
    public ProblemDetail tratarGenerico(Exception ex) {
        log.error("Erro inesperado", ex);
        ProblemDetail pd = ProblemDetail.forStatusAndDetail(
                HttpStatus.INTERNAL_SERVER_ERROR, "Erro interno. Tente novamente mais tarde.");
        pd.setTitle("Erro interno");
        return pd;
    }
}
```

Resposta de um `POST /tarefas` com título em branco:

```json
{
  "type": "about:blank",
  "title": "Erro de validação",
  "status": 400,
  "detail": "Dados inválidos",
  "instance": "/tarefas",
  "erros": { "titulo": "O título é obrigatório" }
}
```

Pontos importantes:

- Estender `ResponseEntityExceptionHandler` faz o handler genérico `Exception` **não** engolir exceções do próprio Spring (método HTTP não suportado, JSON malformado...), que continuam com o status correto.
- Crie uma classe base (`RecursoNaoEncontradoException`) da qual `UsuarioNaoEncontradoException`, `TarefaNaoEncontradaException` etc. herdam, para ter **um handler só** para todos os 404.
- O handler genérico é obrigatório: sem ele, qualquer bug vira uma página de erro padrão com detalhes internos.
- Na Unidade IX você vai adicionar um handler para `AccessDeniedException` (403); sem ele, o handler genérico a transformaria em 500.

---

## 7. CORS

Para o front-end (Unidade V, seção CORS) conseguir chamar a API, configure as origens permitidas **no back-end**:

```java
@Configuration
public class WebConfig implements WebMvcConfigurer {

    @Override
    public void addCorsMappings(CorsRegistry registry) {
        registry.addMapping("/**")
                .allowedOrigins("http://localhost:3000")
                .allowedMethods("GET", "POST", "PUT", "PATCH", "DELETE", "OPTIONS")
                .allowedHeaders("*");
    }
}
```

Nunca use `allowedOrigins("*")` em produção em APIs com autenticação. Liste as origens reais.

---

## 8. Documentando com OpenAPI (Swagger)

O front-end e outros times consomem sua API pela **documentação**. A biblioteca **springdoc-openapi** gera o contrato OpenAPI e uma interface interativa a partir dos seus Controllers, sem configuração:

```xml
<dependency>
    <groupId>org.springdoc</groupId>
    <artifactId>springdoc-openapi-starter-webmvc-ui</artifactId>
    <version><!-- use a versão estável mais recente em springdoc.org --></version>
</dependency>
```

Ao subir a aplicação, a interface fica em `http://localhost:8080/swagger-ui.html` e o contrato em `/v3/api-docs`. Consulte [springdoc.org](https://springdoc.org) para a versão compatível com o seu Spring Boot. Você pode complementar com `@Operation(summary = "...")` e `@ApiResponse` nos métodos.

---

## 9. Testando a API

Teste o Controller **isolado** com `@WebMvcTest` e `MockMvc`: sobe só a camada web, sem banco, com o Service substituído por um *mock*.

```java
@WebMvcTest(TarefaController.class)
class TarefaControllerTest {

    @Autowired private MockMvc mockMvc;

    @MockitoBean private TarefaService service;      // @MockBean em versões do Boot anteriores à 3.4

    @Test
    void deveRetornar404QuandoTarefaNaoExiste() throws Exception {
        when(service.buscarPorId(99L)).thenThrow(new TarefaNaoEncontradaException(99L));

        mockMvc.perform(get("/tarefas/99"))
                .andExpect(status().isNotFound())
                .andExpect(jsonPath("$.title").value("Recurso não encontrado"));
    }

    @Test
    void deveRetornar400QuandoTituloEmBranco() throws Exception {
        String json = """
                { "titulo": "", "usuarioId": 1 }
                """;

        mockMvc.perform(post("/tarefas")
                        .contentType(MediaType.APPLICATION_JSON)
                        .content(json))
                .andExpect(status().isBadRequest())
                .andExpect(jsonPath("$.erros.titulo").exists());
    }
}
```

Esse tipo de teste valida **rota, status code, validação e formato de erro** sem subir banco. Complemente com os testes de Service (Unidade VI) e de Repository (Unidade VII), e com a coleção do Postman da Unidade V apontada para a sua API.

---

## 10. Conteúdo em Vídeo

- 🎥 [Vídeo: Spring Web / Spring MVC na prática](https://www.youtube.com/watch?v=YuzWGKSzEcE&t=2707s&pp=ygUJc291emEgZGV2) (começa no meio do vídeo; se o trecho não fizer sentido sozinho, assista desde o início)

!!! warning "Confira a versão do vídeo"
    Se o vídeo expõe entidades direto no Controller ou trata erros devolvendo `String`, siga o padrão deste material (DTOs e `ProblemDetail`).

---

## 11. Checklist de Conclusão

- [ ] Descrevo o caminho de uma requisição (DispatcherServlet, Handler Mapping, conversor).
- [ ] Explico ao menos três motivos para não expor entidades e crio `Request`/`Response` em Records.
- [ ] Devolvo `201` + `Location` no `POST`, `204` no `DELETE` e `200` nos demais.
- [ ] Valido a entrada com `@Valid` e recebo `400` com a lista de campos inválidos.
- [ ] Meu `@RestControllerAdvice` devolve **o mesmo formato** (`ProblemDetail`) para 400, 404, 409 e 500.
- [ ] Nenhum Controller tem `try/catch` de regra de negócio.
- [ ] A API tem Swagger acessível e CORS configurado.
- [ ] Escrevi testes com `MockMvc` para um caminho feliz e dois de erro.

---

## 12. Projeto Prático

Construa a API REST completa do **Gerenciador de Tarefas**, sobre a persistência da Unidade VII:

- 🌐 [Instruções do Mini Projeto 5: Spring Web ➔](desafio.md#mini-projeto-5)
