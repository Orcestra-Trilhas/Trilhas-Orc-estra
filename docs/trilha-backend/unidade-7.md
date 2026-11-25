# Unidade VII: Spring Data JPA

---

## 1. Visão Geral

Aqui você entende **como a persistência funciona por baixo dos panos**: o que é ORM, a relação entre JPA, Hibernate e Spring Data JPA, como mapear entidades e relacionamentos, como funcionam os repositórios e como escrever consultas personalizadas, incluindo os problemas clássicos (N+1, `LazyInitializationException`) e como enxergá-los.

Ao final, você será capaz de mapear classes Java para tabelas, modelar relacionamentos e herança, e escolher a melhor forma de consultar dados.

!!! note "Pré-requisitos"
    Esta unidade usa SQL, `JOIN` e transações (Unidade IV), `Optional` (Unidade III), `equals`/`hashCode` (Unidade II) e a arquitetura em camadas (Unidade VI). Nesta etapa você substitui o repositório em memória da Unidade VI por um `JpaRepository`.

---

## 2. Fundamentos

### 7.1 ORM: O Problema que o JPA Resolve

**ORM (Object-Relational Mapping)** mapeia objetos (classes Java) para tabelas e vice-versa. Resolve o **"impedance mismatch"**: bancos relacionais trabalham com tabelas, linhas e chaves estrangeiras; o código orientado a objetos trabalha com classes, atributos e referências. Sem ORM, você escreveria SQL e converteria cada `ResultSet` em objetos linha por linha. O ORM automatiza a conversão nos dois sentidos.

### 7.2 JPA, Hibernate e Spring Data JPA: Quem é Quem

- **JPA (Jakarta Persistence API):** apenas uma **especificação** (interfaces e anotações como `@Entity`, `EntityManager`). Define *o quê*, não implementa nada.
- **Hibernate:** a **implementação** mais usada da JPA. É ele quem traduz objetos em SQL e executa no banco.
- **Spring Data JPA:** camada **acima** que elimina código repetitivo: você declara uma **interface** e o Spring gera a implementação em tempo de execução.

Cadeia: você programa contra o **Spring Data JPA** → que usa a **JPA** como contrato → implementada pelo **Hibernate** → que gera o **SQL**.

!!! warning "`javax` vs `jakarta`"
    No Spring Boot 3, os imports são `jakarta.persistence.*`. Tutoriais antigos usam `javax.persistence.*` e **não compilam** no Boot 3.

### 7.3 Configuração e Visualizando o SQL

Dependências: `spring-boot-starter-data-jpa` e `com.h2database:h2`. No `application.properties`:

```properties
# Mostra o SQL que o Hibernate executa (essencial para aprender e para achar o N+1)
spring.jpa.show-sql=true
spring.jpa.properties.hibernate.format_sql=true

# Console web do H2 (desabilitado por padrão)
spring.h2.console.enabled=true
# acesse http://localhost:8080/h2-console  (JDBC URL: veja o log de inicialização)

# O Hibernate cria/atualiza as tabelas a partir das entidades
spring.jpa.hibernate.ddl-auto=update
```

`ddl-auto`: `none` (não mexe), `validate` (só confere), `update` (ajusta o esquema), `create-drop` (cria ao subir, apaga ao parar). `update` serve para estudo; em produção o esquema é versionado por migrations.

Dica: o log de inicialização mostra a URL JDBC do H2 em memória (algo como `jdbc:h2:mem:...`). Para fixá-la, defina `spring.datasource.url=jdbc:h2:mem:tarefasdb`.

### 7.4 Mapeando Entidades

Uma **Entidade** é uma classe Java que representa uma tabela:

- **`@Entity`**: marca a classe como entidade.
- **`@Table(name = "tarefas")`**: nome da tabela (opcional).
- **`@Id`**: chave primária.
- **`@GeneratedValue(strategy = GenerationType.IDENTITY)`**: o banco gera o ID (auto-incremento).
- **`@Column(nullable = false, length = 150)`**: customiza a coluna.
- **`@Enumerated(EnumType.STRING)`**: como gravar enums.

!!! danger "Sempre use `EnumType.STRING` em enums"
    O padrão é `ORDINAL`, que grava a **posição** (0, 1, 2). Se você reordenar ou inserir um valor no enum, os dados antigos passam a significar outra coisa, sem nenhum erro. Com `STRING` grava-se o nome (`"ALTA"`).

**Regras de uma entidade:** precisa de **construtor sem argumentos** (pode ser `protected`), **não pode ser `final`** e **não pode ser um Record**, porque o Hibernate precisa instanciá-la e alterá-la. (Por isso a trilha usa classes para entidades e Records para DTOs.)

```java
@Entity
@Table(name = "usuarios")
public class Usuario {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(nullable = false, length = 100)
    private String nome;

    @Column(nullable = false, unique = true, length = 150)
    private String email;

    @OneToMany(mappedBy = "usuario", cascade = CascadeType.ALL, orphanRemoval = true)
    private List<Tarefa> tarefas = new ArrayList<>();

    protected Usuario() { }   // exigido pelo JPA

    public Usuario(String nome, String email) {
        this.nome = nome;
        this.email = email;
    }

    // Mantém os dois lados da relação consistentes
    public void adicionarTarefa(Tarefa tarefa) {
        tarefas.add(tarefa);
        tarefa.setUsuario(this);
    }

    // getters e setters necessários
}
```

!!! warning "Lombok em entidades"
    `@Data` em uma entidade gera `equals`, `hashCode` e `toString` que percorrem **todos** os campos, inclusive as relações, o que causa recursão infinita em relações bidirecionais e dispara carregamentos inesperados. Nesta trilha o Lombok é opcional; se usar, prefira `@Getter`/`@Setter` e escreva `equals`/`hashCode` à mão (baseados no `id` ou em uma chave de negócio como o e-mail) e **não** inclua coleções no `toString`.

---

### 7.5 Relacionamentos Entre Entidades

- **`@ManyToOne` / `@OneToMany`** (1:N): `Tarefa` tem `@ManyToOne` para `Usuario` (é o lado que guarda a FK); `Usuario` tem `@OneToMany` de volta.
- **`@OneToOne`** (1:1): ex.: `Usuario` e `Perfil`.
- **`@ManyToMany`** (N:N): ex.: `Tarefa` e `Tag`, com tabela associativa declarada em `@JoinTable`.
- **`@JoinColumn(name = "usuario_id")`**: nome da coluna FK, no lado "dono".

**Lado dono e `mappedBy`:** em uma relação bidirecional, **apenas um lado é o dono** (o que tem a FK, o `@ManyToOne`). O outro lado deve declarar **`mappedBy`** com o nome do atributo do lado dono. Sem `mappedBy`, o Hibernate trata os dois lados como relações independentes e cria uma **tabela de junção extra** que você não pediu.

```java
@Entity
@Table(name = "tarefas")
@Inheritance(strategy = InheritanceType.SINGLE_TABLE)
public class Tarefa {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(nullable = false, length = 150)
    private String titulo;

    private String descricao;
    private boolean concluida;

    @ManyToOne(fetch = FetchType.LAZY, optional = false)
    @JoinColumn(name = "usuario_id")
    private Usuario usuario;

    @ManyToMany
    @JoinTable(
        name = "tarefa_tag",
        joinColumns = @JoinColumn(name = "tarefa_id"),
        inverseJoinColumns = @JoinColumn(name = "tag_id"))
    private Set<Tag> tags = new HashSet<>();

    protected Tarefa() { }
    // construtor, getters e setters
}
```

**`cascade` e `orphanRemoval`** (no lado `@OneToMany`):
- `cascade = CascadeType.ALL`: operações no pai (salvar, remover) se propagam aos filhos.
- `orphanRemoval = true`: um filho removido da lista do pai é **apagado** do banco.
- Use cascade quando o filho **não faz sentido sem o pai** (tarefas de um usuário). **Não** use em `@ManyToMany` com `REMOVE`: apagar uma tarefa apagaria tags compartilhadas.

### 7.6 Herança em JPA

A estratégia é escolhida com `@Inheritance`:

| Estratégia | Resultado |
| :--- | :--- |
| `SINGLE_TABLE` (padrão) | uma tabela, com coluna discriminadora (`DTYPE`) e colunas das subclasses anuláveis |
| `JOINED` | uma tabela por classe, ligadas por PK/FK (exige JOIN) |
| `TABLE_PER_CLASS` | uma tabela completa por classe concreta (consultas polimórficas complicadas) |

```java
@Entity
public class TarefaComPrazo extends Tarefa {

    private LocalDate prazo;

    @Enumerated(EnumType.STRING)
    private Prioridade prioridade;

    protected TarefaComPrazo() { }
    // construtor, getters
}
```

Um `tarefaRepository.findAll()` devolve `Tarefa` **e** `TarefaComPrazo` na mesma lista (polimorfismo). Como as colunas `prazo` e `prioridade` são `NULL` nas tarefas simples, **não** marque essas colunas como `nullable = false`.

---

### 7.7 Estratégias de Carregamento: LAZY vs. EAGER

- **`FetchType.LAZY`** (padrão em `@OneToMany` e `@ManyToMany`): carrega os dados relacionados só quando acessados.
- **`FetchType.EAGER`** (padrão em `@ManyToOne` e `@OneToOne`): carrega junto com a entidade.

!!! tip "Recomendação"
    Declare **`fetch = FetchType.LAZY` também em `@ManyToOne` e `@OneToOne`**, e carregue explicitamente (com `JOIN FETCH` ou `@EntityGraph`) quando precisar. O `EAGER` padrão é uma das principais causas de N+1 e de consultas desnecessariamente pesadas.

**Problema N+1:** ao buscar `N` entidades e depois acessar uma relação `LAZY` de cada uma, o Hibernate dispara **1 consulta pela lista + N consultas** (uma por entidade). Com `spring.jpa.show-sql=true` você enxerga isso no console: se ao listar 50 usuários aparecerem 51 `select`, é N+1. Soluções:

```java
// JOIN FETCH em JPQL
@Query("SELECT DISTINCT u FROM Usuario u JOIN FETCH u.tarefas")
List<Usuario> findAllComTarefas();

// ou @EntityGraph, sem escrever JPQL
@EntityGraph(attributePaths = "tarefas")
List<Usuario> findAll();
```

!!! warning "`JOIN FETCH` de coleção com paginação"
    Combinar `JOIN FETCH` de uma coleção com `Pageable` faz o Hibernate paginar **em memória** (e avisa no log). Para listas paginadas, evite fazer fetch de coleções.

**`LazyInitializationException`:** é o primeiro erro que você vai ver. Ocorre ao acessar uma relação `LAZY` **depois** que a sessão do Hibernate foi fechada (tipicamente no Controller, após o Service ter terminado). Soluções corretas: carregar o que precisa dentro do Service (`JOIN FETCH`, `@EntityGraph`) e **converter a entidade em DTO dentro da transação**. Não resolva trocando tudo para `EAGER`.

!!! note "`open-in-view`"
    O Spring Boot liga `spring.jpa.open-in-view=true` por padrão, o que mantém a sessão aberta até a resposta e **mascara** o `LazyInitializationException` em desenvolvimento, e ele aparece depois. A recomendação é desligar (`spring.jpa.open-in-view=false`) e converter para DTO no Service.

---

### 7.8 Contexto de Persistência e Dirty Checking

O Hibernate mantém um **contexto de persistência**: um "cache de sessão" onde ficam as entidades carregadas. Uma entidade pode estar em quatro estados:

- **Transient/new:** criada com `new`, o Hibernate não a conhece.
- **Managed:** carregada ou salva na sessão atual; mudanças são **monitoradas**.
- **Detached:** já foi gerenciada, mas a sessão fechou.
- **Removed:** marcada para exclusão.

**Dirty checking:** dentro de uma transação, ao alterar uma entidade *managed*, o Hibernate detecta a mudança e emite o `UPDATE` **sozinho** no `commit`, sem você chamar `save`:

```java
@Transactional
public void concluir(Long id) {
    Tarefa tarefa = repository.findById(id).orElseThrow(() -> new TarefaNaoEncontradaException(id));
    tarefa.setConcluida(true);   // nenhum save() necessário: o UPDATE sai no commit
}
```

Chamar `save` nesse caso é inofensivo, mas desnecessário. Entender isso evita a confusão de "por que o objeto foi salvo sem eu pedir?".

---

### 7.9 Repositórios do Spring Data JPA

Você declara uma **interface** e o Spring gera a implementação (via *proxy*) em tempo de execução. A hierarquia (Spring Data 3.x):

- **`CrudRepository<T, ID>`**: CRUD básico (`save`, `findById`, `findAll`, `deleteById`...).
- **`ListCrudRepository<T, ID>`**: igual, mas `findAll` devolve `List` em vez de `Iterable`.
- **`PagingAndSortingRepository<T, ID>`** (e `ListPagingAndSortingRepository`): paginação (`Pageable`) e ordenação (`Sort`). A partir do Spring Data 3, **não** estende mais `CrudRepository`.
- **`JpaRepository<T, ID>`**: a mais usada; combina as interfaces `List...` acima e acrescenta métodos de JPA (`flush()`, `saveAndFlush()`, operações em lote).

```java
public interface TarefaRepository extends JpaRepository<Tarefa, Long> { }
```

Com só isso você já usa `save`, `findById` (retorna `Optional<Tarefa>`), `findAll`, `deleteById` e `count`.

---

### 7.10 Consultas Personalizadas

#### 7.10.1 Query Methods (derivadas do nome)

```java
List<Tarefa> findByConcluida(boolean concluida);
List<Tarefa> findByTituloContainingIgnoreCase(String texto);
List<Tarefa> findByUsuarioIdOrderByTituloAsc(Long usuarioId);
long countByUsuarioIdAndConcluida(Long usuarioId, boolean concluida);
boolean existsByEmail(String email);   // em UsuarioRepository
```

Palavras-chave: `findBy`, `countBy`, `existsBy`, `And`, `Or`, `Containing`, `IgnoreCase`, `OrderBy`, `GreaterThan`, `Between`. Se o nome estiver errado (atributo inexistente), a aplicação **falha ao subir**, o que ajuda a detectar erros cedo. Quando o nome fica longo demais, passe para `@Query`.

#### 7.10.2 `@Query` (JPQL ou SQL nativo)

**JPQL** parece SQL, mas opera sobre **entidades e atributos**, não tabelas e colunas:

```java
@Query("SELECT t FROM Tarefa t WHERE t.concluida = false AND t.usuario.id = :usuarioId")
List<Tarefa> buscarPendentesPorUsuario(@Param("usuarioId") Long usuarioId);

@Query("SELECT t.concluida, COUNT(t) FROM Tarefa t WHERE t.usuario.id = :id GROUP BY t.concluida")
List<Object[]> estatisticasPorUsuario(@Param("id") Long id);
```

Com `@Query(nativeQuery = true, value = "SELECT ...")` você escreve SQL nativo quando precisa de um recurso específico do banco (perde a portabilidade).

#### 7.10.3 Paginação e Ordenação

```java
Page<Tarefa> findByConcluida(boolean concluida, Pageable pageable);

// uso no Service
Pageable pagina = PageRequest.of(0, 10, Sort.by("titulo").ascending());
Page<Tarefa> resultado = repository.findByConcluida(false, pagina);
resultado.getContent();        // os itens da página
resultado.getTotalElements();  // total geral
resultado.getTotalPages();
```

Listar tudo de uma tabela grande é um erro de produção; paginar é o padrão.

#### 7.10.4 `Specification` (consultas dinâmicas)

Para buscas com **filtros opcionais** (que não cabem em um nome de método fixo), estenda também `JpaSpecificationExecutor`:

```java
public interface TarefaRepository
        extends JpaRepository<Tarefa, Long>, JpaSpecificationExecutor<Tarefa> { }

public class TarefaSpecs {
    public static Specification<Tarefa> concluida(Boolean valor) {
        return (root, query, cb) -> valor == null ? null : cb.equal(root.get("concluida"), valor);
    }
    public static Specification<Tarefa> tituloContem(String texto) {
        return (root, query, cb) -> texto == null ? null
                : cb.like(cb.lower(root.get("titulo")), "%" + texto.toLowerCase() + "%");
    }
}

// uso: filtros nulos são ignorados
repository.findAll(Specification.where(concluida(filtroConcluida)).and(tituloContem(filtroTitulo)), pageable);
```

É a técnica mais avançada: use quando `Query Methods` e `@Query` ficarem limitados.

---

### 7.11 Transações (`@Transactional`)

Operações com várias escritas devem ser uma **transação** (Unidade IV: ACID). O Spring gerencia isso com `@Transactional`, normalmente na camada **Service**:

```java
@Transactional
public void concluirTarefaEAtualizarContador(Long tarefaId) {
    // se qualquer operação aqui lançar exceção, tudo é revertido
}

@Transactional(readOnly = true)
public List<TarefaResponse> listar() { ... }   // dica de otimização para leituras
```

Pontos que causam bugs reais:

- **Rollback só em exceções *unchecked*.** Por padrão, o Spring faz rollback para `RuntimeException` e `Error`. Uma exceção *checked* (`Exception`) **não** reverte a transação. Para reverter, use `@Transactional(rollbackFor = Exception.class)`.
- **Chamada interna não é transacional.** `@Transactional` funciona via proxy: se um método da classe chama **outro método da mesma classe** que tem `@Transactional`, a anotação é ignorada. Chame através de outro Bean.
- Funciona em métodos **`public`**.

---

## 3. Testando a Persistência

`@DataJpaTest` sobe só a camada JPA com um H2 em memória, ideal para testar consultas:

```java
@DataJpaTest
class TarefaRepositoryTest {

    @Autowired private UsuarioRepository usuarioRepository;
    @Autowired private TarefaRepository tarefaRepository;

    @Test
    void deveBuscarApenasPendentesDoUsuario() {
        Usuario u = usuarioRepository.save(new Usuario("Ana", "ana@orcestra.com"));
        // cria e salva uma tarefa pendente e uma concluída para u ...

        List<Tarefa> pendentes = tarefaRepository.buscarPendentesPorUsuario(u.getId());

        assertEquals(1, pendentes.size());
    }
}
```

Cada teste roda dentro de uma transação que é revertida ao final, então os testes não interferem uns nos outros.

---

## 4. Conteúdo em Vídeo

- 🎥 [Vídeo: Dominando o Spring Data JPA](https://www.youtube.com/watch?v=XgfAabFGGj4&t=5711s&pp=ygUJc291emEgZGV2)
- 📚 [Documentação oficial do Spring Data JPA](https://docs.spring.io/spring-data/jpa/reference/)

!!! warning "Confira a versão do vídeo"
    Vídeos mais antigos usam `javax.persistence` e a hierarquia antiga de repositórios. Se o código do vídeo divergir deste material, siga este material e a documentação oficial.

---

## 5. Checklist de Conclusão

- [ ] Explico a diferença entre JPA, Hibernate e Spring Data JPA.
- [ ] Mapeio um enum com `EnumType.STRING` e explico o risco do `ORDINAL`.
- [ ] Modelo uma relação 1:N bidirecional com `mappedBy`, `cascade` e `orphanRemoval`.
- [ ] Mapeio uma relação N:N com `@JoinTable` e a herança `Tarefa`/`TarefaComPrazo` com `SINGLE_TABLE`.
- [ ] Provoco um N+1 de propósito, vejo no log e corrijo com `JOIN FETCH` ou `@EntityGraph`.
- [ ] Provoco e corrijo uma `LazyInitializationException`.
- [ ] Explico por que uma alteração dentro de `@Transactional` é salva sem `save()`.
- [ ] Escrevo um query method, uma `@Query` JPQL, uma consulta paginada e uma `Specification`.
- [ ] Sei quando o `@Transactional` **não** faz rollback e por que uma chamada interna o ignora.
- [ ] Escrevi ao menos um `@DataJpaTest`.

---

## 6. Projeto Prático

Aplique entidades, relacionamentos, herança e consultas personalizadas na camada de persistência do **Gerenciador de Tarefas**:

- 🌐 [Instruções do Mini Projeto 4: Spring Data JPA ➔](desafio.md#mini-projeto-4)
