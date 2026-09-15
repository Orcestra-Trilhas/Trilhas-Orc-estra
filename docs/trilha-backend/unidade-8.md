# Spring Data JPA – Persistência e ORM

---

## 1. Visão Geral

Nas unidades anteriores, você conheceu o Spring Data JPA de forma superficial, como a camada de persistência do projeto. Aqui, você vai entender **como ele funciona por baixo dos panos**: o que é ORM, qual a relação entre JPA, Hibernate e Spring Data JPA, como mapear entidades, como funcionam os repositórios prontos do Spring e como escrever consultas personalizadas.

Ao final, você será capaz de mapear classes Java para tabelas do banco, modelar relacionamentos entre entidades e escolher a forma mais adequada de consultar dados — sem escrever SQL manualmente na maioria dos casos.

---
## 2. Fundamento do Spring Data JPA

### 8.1 ORM: O Problema que o JPA Resolve

**ORM (Object-Relational Mapping)** é a técnica de mapear objetos do código (classes Java) para tabelas de um banco de dados relacional, e vice-versa. O problema que ele resolve é o chamado **"impedance mismatch"**: bancos relacionais trabalham com tabelas, linhas e chaves estrangeiras, enquanto o código orientado a objetos trabalha com classes, atributos e referências entre objetos — são dois modelos diferentes de representar a mesma informação.

Sem ORM, cada operação de banco exigiria escrever SQL manualmente e converter o resultado (`ResultSet`) em objetos Java na mão, linha por linha. O ORM automatiza essa conversão nos dois sentidos: transforma um objeto em `INSERT`/`UPDATE` e transforma uma linha de tabela de volta em objeto.

### 8.2 JPA, Hibernate e Spring Data JPA: Quem é Quem

É comum confundir esses três nomes, mas eles atuam em camadas diferentes:

- **JPA (Jakarta Persistence API)** — é apenas uma **especificação** (um conjunto de interfaces e anotações, como `@Entity` e `EntityManager`). Ela define *o quê* deve existir, mas não implementa nada sozinha.
- **Hibernate** — é a **implementação** mais usada da especificação JPA. É ele quem de fato traduz seus objetos em SQL e executa as operações no banco. Existem outras implementações (ex.: EclipseLink), mas o Hibernate é o padrão de facto no ecossistema Spring.
- **Spring Data JPA** — é uma camada **acima** do JPA/Hibernate, criada pelo Spring para eliminar código repetitivo. Com ele, você geralmente nem interage diretamente com `EntityManager` ou escreve implementações de repositório — basta declarar uma **interface**, e o Spring Data JPA gera a implementação automaticamente em tempo de execução.

Resumindo a cadeia: você programa contra o **Spring Data JPA** → que usa o **JPA** como contrato → implementado pelo **Hibernate** → que gera o **SQL** de fato.

---

### 8.3 Mapeando Entidades

Uma **Entidade** é uma classe Java que representa uma tabela do banco de dados. O mapeamento básico é feito com anotações:

- **`@Entity`**: marca a classe como uma entidade JPA.
- **`@Table(name = "tarefas")`**: define o nome da tabela (opcional — sem ela, o nome da tabela é o nome da classe).
- **`@Id`**: marca o atributo que é a chave primária.
- **`@GeneratedValue(strategy = GenerationType.IDENTITY)`**: define como o ID é gerado (ex.: `IDENTITY` delega a geração ao próprio banco, como um `AUTO_INCREMENT`).
- **`@Column(name = "descricao", nullable = false)`**: customiza uma coluna (nome, se aceita nulo, tamanho, etc.) — opcional para colunas com mapeamento padrão.

```java
@Entity
@Table(name = "tarefas")
public class Tarefa {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(nullable = false)
    private String descricao;

    private boolean concluida;
}
```

---

### 8.4 Relacionamentos Entre Entidades

O JPA mapeia as cardinalidades vistas na Unidade IV (modelagem) diretamente em anotações:

- **`@OneToMany` / `@ManyToOne`**: relação 1:N. Ex.: um `Usuario` tem várias `Tarefa` (`@OneToMany` no `Usuario`), e cada `Tarefa` pertence a um `Usuario` (`@ManyToOne` na `Tarefa`, lado que efetivamente guarda a chave estrangeira).
- **`@OneToOne`**: relação 1:1, ex.: um `Usuario` e um `PerfilUsuario`.
- **`@ManyToMany`**: relação N:N, ex.: uma `Tarefa` pode ter várias `Tag`, e cada `Tag` pode estar em várias `Tarefa` — o JPA gera automaticamente uma tabela associativa intermediária.
- **`@JoinColumn(name = "usuario_id")`**: usada no lado "dono" da relação (geralmente o lado `@ManyToOne`) para definir o nome da coluna de chave estrangeira.

O lado que possui `@JoinColumn` é o lado "dono" do relacionamento — é ele que efetivamente controla a chave estrangeira no banco.

---

### 8.5 Estratégias de Carregamento: LAZY vs. EAGER

Toda relação entre entidades define **quando** os dados relacionados são carregados do banco:

- **`FetchType.LAZY`** (padrão em `@OneToMany` e `@ManyToMany`): os dados relacionados só são carregados quando efetivamente acessados no código. Mais eficiente, evita carregar dados desnecessários.
- **`FetchType.EAGER`** (padrão em `@ManyToOne` e `@OneToOne`): os dados relacionados são carregados **junto** com a entidade principal, na mesma consulta (ou em consultas adicionais imediatas).

Um cuidado importante aqui é o **problema N+1**: ao buscar uma lista de `N` entidades que têm relações `LAZY` acessadas em seguida, o Hibernate pode disparar 1 consulta para a lista + N consultas adicionais (uma para cada relação acessada), em vez de uma única consulta otimizada. Esse problema é resolvido com `JOIN FETCH` em consultas customizadas (seção 8) quando necessário.

---

### 8.6 Repositórios do Spring Data JPA

Em vez de escrever manualmente uma classe de acesso a dados, o Spring Data JPA gera a implementação a partir de uma **interface**. A hierarquia principal é:

- **`CrudRepository<T, ID>`**: fornece as operações básicas de CRUD (`save`, `findById`, `findAll`, `deleteById`, etc.).
- **`PagingAndSortingRepository<T, ID>`**: estende o `CrudRepository`, adicionando suporte a paginação (`Pageable`) e ordenação (`Sort`).
- **`JpaRepository<T, ID>`**: a interface mais usada na prática — estende `PagingAndSortingRepository`, adicionando métodos específicos do JPA (ex.: `flush()`, `saveAndFlush()`, versões em lote de operações).

```java
public interface TarefaRepository extends JpaRepository<Tarefa, Long> {
}
```

Só com essa declaração (sem nenhuma implementação escrita por você), já é possível usar `tarefaRepository.save(tarefa)`, `tarefaRepository.findById(1L)`, `tarefaRepository.findAll()`, entre outros — o Spring Data JPA gera a implementação real em tempo de execução (via *proxy*).

---

### 8.7 Escrevendo Consultas Personalizadas

Quando os métodos prontos do `JpaRepository` não são suficientes, existem três formas principais de consultar dados:

#### 8.7.1 Query Methods (Consultas Derivadas do Nome)

O Spring Data JPA interpreta o **nome do método** e gera a consulta automaticamente, sem precisar escrever nada além da assinatura:

```java
List<Tarefa> findByConcluida(boolean concluida);
List<Tarefa> findByDescricaoContainingIgnoreCase(String texto);
List<Tarefa> findByUsuarioIdOrderByDataCriacaoDesc(Long usuarioId);
```

Palavras-chave como `findBy`, `And`, `Or`, `Containing`, `OrderBy`, `GreaterThan` seguem uma convenção que o Spring Data JPA traduz automaticamente em SQL.

#### 8.7.2 `@Query` (JPQL ou SQL Nativo)

Para consultas mais complexas, é possível escrever a query manualmente usando **JPQL** (uma linguagem parecida com SQL, mas que opera sobre entidades e atributos Java, não sobre tabelas e colunas):

```java
@Query("SELECT t FROM Tarefa t WHERE t.concluida = false AND t.usuario.id = :usuarioId")
List<Tarefa> buscarPendentesPorUsuario(@Param("usuarioId") Long usuarioId);
```

Também é possível usar `@Query(nativeQuery = true)` para escrever SQL nativo diretamente, quando é necessário usar algum recurso específico do banco.

#### 8.7.3 `Specification` (Consultas Dinâmicas)

Para casos onde os filtros variam dinamicamente (ex.: uma busca com múltiplos filtros opcionais), o Spring Data JPA oferece a interface `JpaSpecificationExecutor`, permitindo montar consultas programaticamente em vez de fixá-las em uma anotação ou nome de método. É uma técnica mais avançada, indicada quando `Query Methods` e `@Query` ficam limitados.

---

### 8.8 Transações (`@Transactional`)

Operações que envolvem múltiplas escritas no banco (ex.: salvar uma `Tarefa` e atualizar um contador no `Usuario`) devem ser tratadas como uma **transação** — ou tudo é aplicado, ou nada é (rollback em caso de erro). O Spring gerencia isso com a anotação `@Transactional`, geralmente aplicada na camada de **Service**:

```java
@Transactional
public void concluirTarefaEAtualizarContador(Long tarefaId) {
    // se qualquer operação aqui lançar exceção, tudo é revertido
}
```

---

## 3. Conteúdo em Vídeo

- 🎥 [Vídeo: Dominando o Spring Data JPA](https://www.youtube.com/watch?v=XgfAabFGGj4&t=5711s&pp=ygUJc291emEgZGV2)

---

## 11. Projeto Prático

Aplique JpaRepository, relacionamentos e consultas personalizadas na camada de persistência do **Gerenciador de Tarefas**:

- 🌐 [Instruções do Mini Projeto Spring Data JPA ➔](desafio.md#mini-projeto-spring-data-jpa)
