# Unidade VI: Spring Boot

---

## 1. Visão Geral

Agora que você domina Java, banco de dados e HTTP, é hora de entrar no ecossistema Spring. Nesta unidade você entende os conceitos centrais do **Spring Framework** (Inversão de Controle, Injeção de Dependência, Beans e Contexto), como o **Spring Boot** simplifica seu uso, e como estruturar um projeto em **camadas**.

Aqui você monta o **esqueleto** da aplicação: camadas, configuração, logs e um primeiro teste. A persistência com banco entra na Unidade VII, e os Controllers completos (DTOs, validação, erros) na Unidade VIII. Por isso, nesta unidade o repositório guarda os dados **em memória**.

---

## 2. Fundamentos do Spring Framework

O **Spring Framework** é, na essência, um framework de **injeção de dependência**: ele gerencia a criação e o ciclo de vida dos objetos da aplicação, para você não espalhar `new` pelo código.

### 6.1 Inversão de Controle (IoC)

Tradicionalmente, a sua classe decide *quando* e *como* criar suas dependências (um `TarefaService` fazendo `new TarefaRepository()`). Com **Inversão de Controle**, essa responsabilidade passa ao **framework**. Sua classe apenas *declara* do que precisa, e o Spring fornece.

### 6.2 Injeção de Dependência (DI)

É o **mecanismo** pelo qual a IoC acontece: o Spring "injeta" as dependências em vez de a classe criá-las. Formas comuns:

- **Injeção via construtor** (recomendada): dependências como parâmetros do construtor.
- **Injeção via campo** (`@Autowired` no atributo): mais curta, porém menos recomendada (dificulta testes e esconde dependências).

```java
@Service
public class UsuarioService {

    private final UsuarioRepository repository;

    // Com um único construtor, o @Autowired é dispensável
    public UsuarioService(UsuarioRepository repository) {
        this.repository = repository;
    }
}
```

Vantagens do construtor: a dependência é `final` (imutável), a classe nunca existe "pela metade" e nos testes você passa um objeto falso (*mock*) diretamente.

### 6.3 Beans

Um **Bean** é um objeto cujo ciclo de vida é **gerenciado pelo Spring**. Uma classe vira candidata a Bean com os estereótipos `@Component`, `@Service`, `@Repository`, `@Controller`/`@RestController`, ou declarada em uma classe de configuração com `@Bean`:

```java
@Configuration
public class AppConfig {
    @Bean
    public Clock clock() {
        return Clock.systemDefaultZone();
    }
}
```

Por padrão, um Bean é um **singleton**: existe **uma única instância** compartilhada em toda a aplicação. Por isso, Beans (Services, Controllers) **não devem guardar estado mutável** por usuário em atributos.

### 6.4 Component Scanning

O Spring localiza as classes que devem virar Beans varrendo os pacotes do projeto em busca de `@Component` e suas especializações (`@Service`, `@Repository`, `@Controller`).

!!! warning "A varredura começa no pacote da classe principal"
    O scan cobre o pacote da classe `@SpringBootApplication` **e os subpacotes dele**. Se você criar uma classe `@Service` em um pacote irmão (fora dessa árvore), ela **não** é encontrada e você recebe um erro de "bean not found". Mantenha tudo abaixo do pacote principal (ex.: `com.orcestra.tarefas`).

### 6.5 Contexto de Aplicação (ApplicationContext)

O **`ApplicationContext`** é o "container" central: instancia, configura e armazena todos os Beans, e é dele que eles são retirados (injetados). Pense nele como o registro vivo de tudo que o Spring gerencia durante a execução.

---

## 3. Spring Boot: Simplificando o Spring

O **Spring Boot** não é outro framework: é uma camada **sobre** o Spring Framework que reduz configuração manual. IoC, DI, Beans e Contexto continuam sendo os mesmos; o Boot só automatiza a configuração.

### 6.6 A Classe Principal e `@SpringBootApplication`

```java
@SpringBootApplication
public class TarefasApplication {
    public static void main(String[] args) {
        SpringApplication.run(TarefasApplication.class, args);
    }
}
```

`@SpringBootApplication` é uma anotação "guarda-chuva" que combina três:

- `@Configuration`: a classe pode declarar Beans com `@Bean`.
- `@ComponentScan`: ativa a varredura de componentes a partir deste pacote.
- `@EnableAutoConfiguration`: liga a auto-configuração (seção 6.8).

`SpringApplication.run(...)` cria o `ApplicationContext`, executa a varredura e sobe o servidor embutido.

### 6.7 Starters

Um **Starter** é um pacote de dependências pré-configurado para um propósito. Em vez de descobrir quais bibliotecas são compatíveis, você adiciona uma dependência (ex.: `spring-boot-starter-web`) e ela traz tudo, com versões testadas entre si.

- **`spring-boot-starter-web`**: APIs REST (Spring MVC + Jackson para JSON + Tomcat embutido).
- **`spring-boot-starter-data-jpa`**: persistência com JPA/Hibernate.
- **`spring-boot-starter-validation`**: Bean Validation (`@NotBlank`, `@Valid`...). **Não vem** dentro do starter web.
- **`spring-boot-starter-security`**: autenticação e autorização.
- **`spring-boot-starter-test`**: JUnit, Mockito, AssertJ, MockMvc.

Isso resolve o "dependency hell" do Spring clássico, em que cada biblioteca era versionada manualmente.

### 6.8 Auto-Configuração (Auto-Configuration)

O Spring Boot **configura Beans automaticamente** com base no que encontra no classpath:

1. Ao subir, analisa quais dependências (jars) estão presentes.
2. Para cada uma que reconhece, existe uma classe de auto-configuração condicional (`@Conditional...`), ativada **somente se** certas condições forem atendidas (ex.: "se o driver do H2 está no classpath **e** não há um `DataSource` definido por você, crie um para H2 em memória").
3. A auto-configuração **nunca sobrescreve** o que você configurou: ela só preenche o que falta ("configuração sensata por padrão, sempre sobrescrevível").

Para ver o que foi ou não auto-configurado, rode a aplicação com a propriedade `debug=true` (imprime o relatório de condições).

### 6.9 Servidores Embutidos

Antes, uma aplicação Java web virava um `.war` implantado em um Tomcat instalado à parte. O Boot inverte: o servidor (por padrão **Tomcat**; também Jetty ou Undertow) vem **dentro do `.jar`**. Executar a aplicação é `java -jar aplicacao.jar`, e ela já responde em `localhost:8080`.

### 6.10 Configuração: `application.properties` / `application.yml`

Ajustes da aplicação ficam em `src/main/resources/application.properties` (ou `.yml`):

```properties
server.port=8081
spring.application.name=gerenciador-tarefas
logging.level.com.orcestra=DEBUG
```

**Profiles** permitem configurações diferentes por ambiente. Um arquivo `application-dev.properties` só é carregado quando o profile `dev` está ativo (`spring.profiles.active=dev`). Valores sensíveis (senhas, chaves) **nunca** vão para o Git: leia de **variáveis de ambiente** com `${NOME_DA_VARIAVEL}`:

```properties
minha.chave.secreta=${MINHA_CHAVE}
```

```java
@Value("${minha.chave.secreta}")
private String chave;
```

### 6.11 Logs

Não use `System.out.println` em aplicações Spring. Use o **SLF4J** (já incluso):

```java
private static final Logger log = LoggerFactory.getLogger(UsuarioService.class);

log.info("Usuário criado: id={}", usuario.getId());
log.warn("Tentativa de acesso ao usuário inexistente id={}", id);
log.error("Falha ao salvar usuário", excecao);
```

Níveis, do mais ao menos verboso: `TRACE`, `DEBUG`, `INFO`, `WARN`, `ERROR`. O nível se controla por pacote em `logging.level.*`.

---

## 4. Estrutura em Camadas

!!! note "Sobre o nome 'MVC'"
    Em uma aplicação web clássica, MVC tem **View** (HTML). Uma API REST devolve JSON e não tem View. O que usamos é uma **arquitetura em camadas** inspirada em MVC.

- **Controller:** recebe as requisições HTTP e devolve as respostas. **Sem regra de negócio.**
- **Service:** regra de negócio da aplicação.
- **Repository:** comunicação com o armazenamento de dados.
- **Model:** as classes de domínio (`Usuario`, `Tarefa`...).

Regra de dependência: **Controller → Service → Repository**, sempre nesse sentido. Um Controller nunca fala direto com o Repository.

### 6.12 Seu Primeiro Endpoint (visão mínima)

Para verificar que a aplicação funciona, é suficiente este Controller. Os detalhes (DTOs, validação, status codes, erros) são o tema da Unidade VIII.

```java
@RestController
@RequestMapping("/usuarios")
public class UsuarioController {

    private final UsuarioService service;

    public UsuarioController(UsuarioService service) {
        this.service = service;
    }

    @GetMapping
    public List<Usuario> listar() {
        return service.listar();
    }
}
```

### 6.13 Repositório em Memória (temporário)

Como o JPA só chega na Unidade VII, implemente o repositório com um `Map`. **Use os mesmos nomes de método do Spring Data** (`save`, `findById`, `findAll`, `deleteById`) e retorne `Optional` onde fizer sentido: na Unidade VII você troca esta classe por uma interface `JpaRepository` e o **Service não muda**, o que é uma demonstração prática do valor da arquitetura em camadas.

```java
@Repository
public class UsuarioRepository {

    private final Map<Long, Usuario> banco = new ConcurrentHashMap<>();
    private final AtomicLong sequencia = new AtomicLong(1);

    public Usuario save(Usuario usuario) { /* gera id, guarda no mapa, devolve */ }
    public Optional<Usuario> findById(Long id) { return Optional.ofNullable(banco.get(id)); }
    public List<Usuario> findAll() { return new ArrayList<>(banco.values()); }
    public void deleteById(Long id) { banco.remove(id); }
}
```

---

## 5. Primeiros Testes

O `spring-boot-starter-test` já vem no projeto gerado. Um teste de **unidade** de Service, sem subir o Spring, usando JUnit 5 e Mockito:

```java
@ExtendWith(MockitoExtension.class)
class UsuarioServiceTest {

    @Mock
    private UsuarioRepository repository;

    @InjectMocks
    private UsuarioService service;

    @Test
    void deveLancarExcecaoQuandoUsuarioNaoExiste() {
        when(repository.findById(99L)).thenReturn(Optional.empty());

        assertThrows(UsuarioNaoEncontradoException.class, () -> service.buscarPorId(99L));
    }
}
```

O padrão **Arrange / Act / Assert** (preparar, executar, verificar) organiza qualquer teste. Rode com `./mvnw test`. Esse teste só é possível porque a dependência entra pelo **construtor**.

---

## 6. Artigos

- 🌐 [Artigo: Como conectar o H2 Database com o Spring Boot](https://wpsilva.medium.com/utilizando-banco-de-dados-h2-com-spring-de-forma-r%C3%A1pida-e-simples-6d896e15a4af) (leia na Unidade VII, quando for ligar o banco)

---

## 7. Checklist de Conclusão

- [ ] Explico IoC e DI com minhas palavras e sei por que o construtor é preferível ao `@Autowired` em campo.
- [ ] Digo o que são os três componentes de `@SpringBootApplication`.
- [ ] Sei por que um `@Service` fora do pacote principal não é encontrado.
- [ ] Explico o que é um starter e o que a auto-configuração faz (e que ela não sobrescreve minha configuração).
- [ ] Leio uma propriedade de `application.properties` e de uma variável de ambiente com `@Value`.
- [ ] Uso `log.info` em vez de `System.out.println`.
- [ ] Descrevo a responsabilidade de cada camada e o sentido das dependências.
- [ ] Escrevi e rodei ao menos um teste de Service com Mockito.

---

## 8. Projeto Prático

Monte o esqueleto Spring Boot em camadas do Gerenciador de Tarefas, com repositório em memória:

- 🌐 [Instruções do Mini Projeto 3: Esqueleto Spring Boot ➔](desafio.md#mini-projeto-3)
