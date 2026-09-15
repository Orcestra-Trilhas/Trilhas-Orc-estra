# Unidade III – Java (Estruturas de Dados)

---

## 1. Visão Geral

Nesta unidade intermediária, você aprenderá a trabalhar com as estruturas de dados fundamentais do Java, avançando o nível de manipulação de coleções, funções de ordem superior e novos recursos da linguagem.

Ao final desta unidade, você será capaz de manipular coleções de forma eficiente, escrever código mais conciso com Stream API, criar código reutilizável com Generics, organizar seu projeto em pacotes e representar dados de forma imutável com Records.

---

## 2. Tópicos Abordados

### 3.1 Collections (`List`, `Set`, `Map`)

O Java organiza suas estruturas de dados em torno de três interfaces principais do pacote `java.util`, cada uma com uma regra diferente sobre como os elementos se comportam:

- **`List`** — coleção **ordenada** que permite **elementos duplicados** e acesso por índice.
  - `ArrayList`: implementação baseada em array redimensionável; acesso rápido por índice (O(1)), inserção/remoção no meio é mais custosa.
  - `LinkedList`: implementação baseada em lista duplamente encadeada; inserção/remoção nas pontas é rápida, mas acesso por índice é mais lento (O(n)). Também implementa `Deque`.

- **`Set`** — coleção que **não permite elementos duplicados** e não garante acesso por índice.
  - `HashSet`: implementação baseada em tabela hash; não mantém ordem de inserção, mas é a mais rápida para busca/inserção (O(1) em média).
  - `LinkedHashSet`: como o `HashSet`, mas mantém a ordem de inserção.
  - `TreeSet`: mantém os elementos **ordenados** (ordem natural ou via `Comparator`); operações em O(log n).

- **`Map`** — estrutura de pares **chave-valor**, onde cada chave é única (não é filha de `Collection`, é uma hierarquia à parte).
  - `HashMap`: implementação baseada em tabela hash; não garante ordem.
  - `LinkedHashMap`: mantém a ordem de inserção das chaves.
  - `TreeMap`: mantém as chaves ordenadas.

Além dessas, existe a interface **`Queue`** (e sua extensão `Deque`), usada para representar filas (FIFO) ou filas duplas, com implementações como `ArrayDeque` e a própria `LinkedList`.

- [Vídeo: Collections em Java (List, Set, Map, Queue)](https://youtu.be/0JXjsQUoAT8?si=isWHRzmND0u8Mz1T)

---

### 3.2 Stream API

A Stream API é um conjunto de recursos que facilita a **manipulação de estruturas de dados** de forma funcional e declarativa, ou seja, você descreve *o que* quer fazer com os dados, sem escrever manualmente o *como* (loops, contadores, condicionais aninhadas).

O método `stream()` é um método default da interface **`Collection`** (superinterface comum de `List` e `Set`, que por sua vez estende `Iterable`). Como `Map` não é uma `Collection`, ele expõe suas próprias vias de acesso a streams: `map.keySet().stream()`, `map.values().stream()` ou `map.entrySet().stream()`.

Toda stream segue a mesma estrutura de três partes:

**a) Fonte (Source)**
É a origem dos dados — geralmente uma coleção (`lista.stream()`), um array (`Arrays.stream(array)`) ou valores gerados (`Stream.of(...)`). A stream em si **não armazena dados**, apenas referencia a fonte e define um pipeline de processamento.

**b) Operações Intermediárias**
São operações que transformam a stream e retornam **outra stream**, permitindo encadeamento. São **preguiçosas** (lazy) — só são executadas quando uma operação terminal é chamada.
- `filter(Predicate)`: seleciona elementos que atendem a uma condição. Ex.: `.filter(p -> p.getIdade() > 18)`.
- `map(Function)`: transforma cada elemento em outro valor/tipo. Ex.: `.map(Pessoa::getNome)`.
- `sorted()`: ordena os elementos da stream. Ex.: `.sorted(Comparator.comparing(Pessoa::getIdade))`.

**c) Operação Terminal (Terminal Operation)**
É a operação que **consome** a stream e produz um resultado final (ou efeito colateral). Depois dela, a stream não pode mais ser reutilizada.
- `collect(Collectors.toList())`: reúne os elementos processados em uma nova coleção.
- `forEach(Consumer)`: executa uma ação para cada elemento restante. Ex.: `.forEach(System.out::println)`.
- `count()` / `sum()` / `reduce()`: produzem um valor agregado a partir dos elementos da stream.

- [Vídeo: Stream API em Java (fonte, operações intermediárias e terminais)](https://www.youtube.com/watch?v=iovHVVwgfYo)

---

### 3.3 Generics

Generics permitem que classes, interfaces e métodos operem sobre **tipos parametrizados** (`<T>`, `<E>`, `<K, V>`), definidos apenas no momento do uso, em vez de fixados na implementação.

Isso resolve dois problemas: evita a necessidade de duplicar código para cada tipo (ex.: uma `ListaDeInteiros` e uma `ListaDeStrings` separadas) e garante **segurança de tipos em tempo de compilação** — sem Generics, seria preciso usar `Object` e fazer casts manuais, correndo risco de `ClassCastException` em tempo de execução.

Exemplo: `List<String> nomes = new ArrayList<>();` garante, em tempo de compilação, que só `String` pode ser inserida ali. O mesmo mecanismo é usado em classes próprias, como `class Caixa<T> { private T conteudo; }`, e em métodos genéricos, como `<T> T primeiroElemento(List<T> lista)`.

- [Vídeo: Generics em Java](https://youtu.be/OAk6MjD96gM?si=R8p8JT2oRngYklbc)

---

### 3.4 Packages

Packages são o mecanismo do Java para **organizar classes em uma estrutura hierárquica de pastas/namespaces**, geralmente seguindo o padrão de domínio invertido (ex.: `com.empresa.projeto.modulo`).

Além de organizar o código, packages resolvem **conflitos de nomes** (duas classes `Usuario` podem coexistir em pacotes diferentes) e controlam a **visibilidade** entre classes: um membro sem modificador de acesso (visibilidade *default*/package-private) só é visível dentro do mesmo pacote, o que é útil para encapsular detalhes internos de um módulo.

- [Vídeo: Packages em Java (organização e visibilidade)](https://youtu.be/rRfas9Q95U0?si=Qv0KjZSISz41PEfs)

---

### 3.5 Records

Records, introduzidos como recurso estável a partir do Java 16, são um tipo especial de classe pensado para representar **dados imutáveis** de forma concisa.

Ao declarar `record Ponto(int x, int y) {}`, o compilador gera automaticamente: construtor com todos os campos, métodos de acesso (`x()`, `y()` — sem o prefixo `get`), além de `equals()`, `hashCode()` e `toString()` já implementados de forma coerente com os dados. Isso elimina o código repetitivo (*boilerplate*) que antes era necessário escrever manualmente ou gerar com bibliotecas como o Lombok.

Records são ideais para modelar objetos de transferência de dados (DTOs), respostas de API ou qualquer estrutura onde o estado não deve mudar após a criação.

- [Vídeo: Records em Java (imutabilidade e boilerplate)](https://youtu.be/lmzpyCNyziI?si=Sv0WEc0w-cRKgJMM)

---

## 3. Aprofundamento Recomendado

Caso queira dominar ainda mais os detalhes específicos e avançados do Java:

- 🎥 [Vídeo: Maratona Java Virado No Jiraya (Playlist Completa)](https://www.youtube.com/watch?v=VKjFuX91G5Q&list=PL62G310vn6nFIsOCC0H-C2infYgwm8SWW)

---

## 4. Evolução do Projeto Prático

A partir do conteúdo aprendido, aprimore o **Gerenciador de Tarefas no Terminal** adicionando buscas, filtros e manipulação de listas:

- 🌐 [Instruções do Mini Projeto Parte 2: Estruturas de Dados ➔](desafio.md#parte-2-estruturas-de-dados-desafios-de-manipulacao-de-listas)
