# Unidade III: Java Moderno e Estruturas de Dados

---

## 1. Visão Geral

Nesta unidade você avança para a Java que se escreve hoje: coleções, **lambdas**, **Stream API**, **`Optional`**, Generics, Packages, Records e **`java.time`**.

Ao final, você será capaz de manipular coleções de forma concisa e segura, organizar o projeto em pacotes, representar dados imutáveis e trabalhar com datas.

---

## 2. Tópicos Abordados

### 3.1 Collections (`List`, `Set`, `Map`)

O Java organiza suas estruturas de dados em torno de três interfaces do pacote `java.util`, cada uma com uma regra diferente:

- **`List`**: coleção **ordenada** que permite **duplicados** e acesso por índice.
  - `ArrayList`: baseada em array redimensionável; acesso por índice O(1), inserção/remoção no meio mais custosa.
  - `LinkedList`: lista duplamente encadeada; inserção/remoção nas pontas é rápida, acesso por índice é O(n). Também implementa `Deque`.

- **`Set`**: coleção que **não permite duplicados**.
  - `HashSet`: tabela hash; sem ordem garantida; busca/inserção O(1) em média.
  - `LinkedHashSet`: como o `HashSet`, mas mantém a ordem de inserção.
  - `TreeSet`: mantém os elementos **ordenados** (ordem natural ou `Comparator`); O(log n).

- **`Map`**: pares **chave-valor**, com chaves únicas (não é filha de `Collection`).
  - `HashMap`: tabela hash; sem ordem garantida.
  - `LinkedHashMap`: mantém a ordem de inserção das chaves.
  - `TreeMap`: mantém as chaves ordenadas.

Existe ainda a interface **`Queue`** (e `Deque`) para filas (FIFO) e filas duplas, com implementações como `ArrayDeque`.

!!! warning "`HashSet` e `HashMap` dependem de `equals` e `hashCode`"
    Essas estruturas decidem se um elemento "já existe" usando `hashCode()` e `equals()` (Unidade II, seção 4.2). Se a sua classe não sobrescreve os dois, dois objetos com os mesmos dados são tratados como **diferentes** e o `Set` aceita "duplicados". Se você sobrescreve só um, a busca falha de forma imprevisível. Esse é um dos bugs mais comuns com chaves de `Map` e elementos de `Set`.

- [Vídeo: Collections em Java (List, Set, Map, Queue)](https://youtu.be/0JXjsQUoAT8?si=isWHRzmND0u8Mz1T)

---

### 3.2 Lambdas e Interfaces Funcionais

Antes da Stream API, você precisa entender o que significa `p -> p.getIdade() > 18`.

Uma **interface funcional** é uma interface com **um único método abstrato**. Uma **lambda** é uma forma curta de implementá-la sem criar uma classe:

```java
// sem lambda
Comparator<Tarefa> porTitulo = new Comparator<>() {
    @Override
    public int compare(Tarefa a, Tarefa b) {
        return a.getTitulo().compareTo(b.getTitulo());
    }
};

// com lambda
Comparator<Tarefa> porTitulo2 = (a, b) -> a.getTitulo().compareTo(b.getTitulo());
```

Sintaxe: `(parâmetros) -> expressão` ou `(parâmetros) -> { instruções; }`. O tipo dos parâmetros é inferido.

As interfaces funcionais que você mais vai usar (pacote `java.util.function`):

| Interface | Método | Para que serve | Exemplo |
| :--- | :--- | :--- | :--- |
| `Predicate<T>` | `boolean test(T t)` | testar uma condição | `t -> t.isConcluida()` |
| `Function<T, R>` | `R apply(T t)` | transformar T em R | `t -> t.getTitulo()` |
| `Consumer<T>` | `void accept(T t)` | executar uma ação | `t -> System.out.println(t)` |
| `Supplier<T>` | `T get()` | fornecer um valor | `() -> new ArrayList<>()` |
| `Comparator<T>` | `int compare(T a, T b)` | comparar para ordenar | `(a, b) -> a.getId().compareTo(b.getId())` |

**Referências a método** (`::`) são atalhos quando a lambda só chama um método existente:

| Forma | Lambda equivalente |
| :--- | :--- |
| `Tarefa::getTitulo` | `t -> t.getTitulo()` |
| `System.out::println` | `x -> System.out.println(x)` |
| `ArrayList::new` | `() -> new ArrayList<>()` |
| `String::valueOf` | `x -> String.valueOf(x)` |

---

### 3.3 Stream API

A Stream API facilita a manipulação de coleções de forma **declarativa**: você descreve *o que* quer, sem escrever o *como* (laços, contadores, condicionais aninhadas).

O método `stream()` é um método default de **`Collection`** (superinterface de `List` e `Set`). Como `Map` não é uma `Collection`, use `map.keySet().stream()`, `map.values().stream()` ou `map.entrySet().stream()`.

Toda stream tem três partes:

**a) Fonte (Source):** `lista.stream()`, `Arrays.stream(array)`, `Stream.of(...)`. A stream **não armazena dados**, só define um pipeline sobre a fonte.

**b) Operações intermediárias:** transformam a stream e retornam **outra stream**. São **preguiçosas** (lazy): só executam quando há uma operação terminal.
- `filter(Predicate)`: `.filter(t -> !t.isConcluida())`
- `map(Function)`: `.map(Tarefa::getTitulo)`
- `sorted(Comparator)`: `.sorted(Comparator.comparing(Tarefa::getTitulo))`

**c) Operação terminal:** **consome** a stream e produz o resultado. Depois dela, a stream não pode ser reutilizada.
- `toList()`: reúne em uma lista (Java 16+). É mais curto que `collect(Collectors.toList())`, mas devolve uma lista **imutável** (chamar `add` nela lança `UnsupportedOperationException`).
- `collect(Collectors.toList())` / `collect(Collectors.groupingBy(...))`: reúne em coleções ou agrupa.
- `forEach(Consumer)`, `count()`, `sum()`, `reduce()`, `anyMatch(Predicate)`, `findFirst()`.

```java
List<String> pendentes = tarefas.stream()
        .filter(t -> !t.isConcluida())
        .sorted(Comparator.comparing(Tarefa::getTitulo))
        .map(Tarefa::getTitulo)
        .toList();

Map<Boolean, Long> contagem = tarefas.stream()
        .collect(Collectors.partitioningBy(Tarefa::isConcluida, Collectors.counting()));
```

Para ordenar por mais de um critério, encadeie: `Comparator.comparing(Tarefa::getTitulo).thenComparing(Tarefa::getId)`.

- [Vídeo: Stream API em Java (fonte, operações intermediárias e terminais)](https://www.youtube.com/watch?v=iovHVVwgfYo)

---

### 3.4 Optional

`Optional<T>` é uma "caixa" que pode ou não conter um valor. Existe para **evitar `null` e `NullPointerException`** em retornos que podem ser vazios. Você vai vê-lo em todo lugar: `repository.findById(id)` devolve um `Optional`.

```java
Optional<Tarefa> achada = tarefas.stream()
        .filter(t -> t.getId().equals(42L))
        .findFirst();

achada.isPresent();                                  // true/false
achada.ifPresent(t -> System.out.println(t));        // executa só se houver valor
String titulo = achada.map(Tarefa::getTitulo)        // transforma, se houver valor
                      .orElse("(sem título)");       // valor padrão
Tarefa t = achada.orElseThrow(() -> new TarefaNaoEncontradaException(42L));
```

Regras práticas:
- Para "não achou, então erro", use `orElseThrow(...)`. É o padrão em services.
- `orElse(x)` **sempre avalia** `x`, mesmo que haja valor. Para valores caros de criar, use `orElseGet(() -> ...)`.
- Não use `Optional` como atributo de classe nem como parâmetro de método. Ele é para **retornos**.
- Evite `optional.get()` sem checar: é um `NullPointerException` disfarçado.

---

### 3.5 Generics

Generics permitem que classes, interfaces e métodos operem sobre **tipos parametrizados** (`<T>`, `<E>`, `<K, V>`), definidos apenas no uso.

Resolvem dois problemas: evitam duplicar código para cada tipo (uma `ListaDeInteiros` e uma `ListaDeStrings`) e garantem **segurança de tipos em tempo de compilação**. Sem Generics, você usaria `Object` e casts manuais, com risco de `ClassCastException` em execução.

```java
List<String> nomes = new ArrayList<>();      // só aceita String

class Caixa<T> { private T conteudo; }

<T> T primeiroElemento(List<T> lista) { return lista.get(0); }
```

Você vai reconhecer Generics em `JpaRepository<Tarefa, Long>` (entidade e tipo do ID) e `ResponseEntity<TarefaResponse>`.

- [Vídeo: Generics em Java](https://youtu.be/OAk6MjD96gM?si=R8p8JT2oRngYklbc)

---

### 3.6 Packages

Packages organizam classes em uma hierarquia de pastas/namespaces, geralmente em domínio invertido (`com.orcestra.tarefas.service`).

Além de organizar, resolvem **conflitos de nomes** (duas classes `Usuario` em pacotes diferentes) e controlam **visibilidade**: um membro sem modificador (*package-private*) só é visível dentro do mesmo pacote.

Nos projetos Spring, os pacotes seguem as camadas:

```text
com.orcestra.tarefas
├── controller
├── service
├── repository
├── model
├── dto
└── exception
```

- [Vídeo: Packages em Java (organização e visibilidade)](https://youtu.be/rRfas9Q95U0?si=Qv0KjZSISz41PEfs)

---

### 3.7 Records

Records (estáveis desde o Java 16) representam **dados imutáveis** de forma concisa.

```java
public record TarefaResumo(Long id, String titulo, boolean concluida) {}
```

O compilador gera: construtor com todos os campos, acessores (`id()`, `titulo()`, sem prefixo `get`), `equals()`, `hashCode()` e `toString()`.

Podem ter validação no construtor compacto e métodos próprios:

```java
public record TarefaResumo(Long id, String titulo, boolean concluida) {
    public TarefaResumo {
        if (titulo == null || titulo.isBlank()) {
            throw new IllegalArgumentException("Título obrigatório");
        }
    }
}
```

**Onde usar Record:** DTOs (objetos de entrada e saída de API), respostas, qualquer estrutura cujo estado não muda depois de criada.

**Onde NÃO usar:** **entidades JPA** (Unidade VII). O JPA precisa de construtor sem argumentos e de objetos mutáveis, e Records são imutáveis. Por isso a trilha adota esta divisão: *entidades* são classes comuns; *DTOs* são Records. Com Records para DTOs, a trilha **não exige Lombok**.

- [Vídeo: Records em Java (imutabilidade e boilerplate)](https://youtu.be/lmzpyCNyziI?si=Sv0WEc0w-cRKgJMM)

---

### 3.8 Datas e Horas (`java.time`)

Nunca use `java.util.Date` em código novo. Use o pacote `java.time`:

| Tipo | Representa | Exemplo |
| :--- | :--- | :--- |
| `LocalDate` | data, sem hora | `2026-12-31` |
| `LocalDateTime` | data e hora, sem fuso | `2026-12-31T18:30:00` |
| `Instant` | momento exato na linha do tempo (UTC) | para timestamps |
| `Duration` / `Period` | intervalo de tempo / de datas | |

```java
LocalDate hoje = LocalDate.now();
LocalDate prazo = LocalDate.of(2026, 12, 31);

boolean atrasada = prazo.isBefore(hoje);
LocalDate daquiA7Dias = hoje.plusDays(7);
long diasRestantes = ChronoUnit.DAYS.between(hoje, prazo);

DateTimeFormatter br = DateTimeFormatter.ofPattern("dd/MM/yyyy");
String texto = prazo.format(br);                    // "31/12/2026"
LocalDate lida = LocalDate.parse("31/12/2026", br);
```

Os objetos de `java.time` são **imutáveis**: `plusDays` devolve um objeto novo. Em JSON, o Spring Boot converte `LocalDate` de/para o formato ISO (`"2026-12-31"`). Esse será o tipo do `prazo` da `TarefaComPrazo`.

- Material oficial: [java.time no dev.java](https://dev.java/learn/date-time/)

---

## 3. Aprofundamento Recomendado

- 🎥 [Vídeo: Maratona Java Virado No Jiraya (Playlist Completa)](https://www.youtube.com/watch?v=VKjFuX91G5Q&list=PL62G310vn6nFIsOCC0H-C2infYgwm8SWW)

---

## 4. Checklist de Conclusão

- [ ] Escolho entre `List`, `Set` e `Map` justificando a escolha.
- [ ] Explico por que um `HashSet<Usuario>` aceita "duplicados" quando falta `equals`/`hashCode`.
- [ ] Converto uma lambda em referência a método e vice-versa.
- [ ] Escrevo um pipeline `filter` + `sorted` + `map` + `toList()` sem ajuda.
- [ ] Uso `Optional` com `orElseThrow` e explico a armadilha do `orElse`.
- [ ] Crio um Record para DTO e explico por que ele não serve como entidade JPA.
- [ ] Calculo quantos dias faltam para um prazo com `LocalDate`.
- [ ] Meu projeto está dividido em pacotes.

---

## 5. Evolução do Projeto Prático

Aprimore o **Gerenciador de Tarefas no Terminal** com buscas, filtros, ordenação e estatísticas:

- 🌐 [Instruções do Mini Projeto 1, Parte 2 ➔](desafio.md#mini-projeto-1-parte-2)
