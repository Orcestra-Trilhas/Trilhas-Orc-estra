# Unidade II: Java (Programação Orientada a Objetos)

---

## 1. Visão Geral

Nesta unidade você aprende a sintaxe básica de Java e o paradigma de **Programação Orientada a Objetos (POO)**: classes, objetos, atributos, métodos, construtores e enums, além dos **4 pilares** (Encapsulamento, Herança, Polimorfismo e Abstração).

Também cobre três temas que as unidades seguintes assumem como conhecidos: **exceções**, **`equals`/`hashCode`/`toString`** e **`instanceof`**.

!!! note "Pré-requisito"
    Esta unidade assume que você já sabe lógica de programação (variáveis, `if`, laços, funções) em alguma linguagem. Ela mostra como isso se escreve em Java, mas não ensina lógica do zero. Para uma referência oficial e gratuita da linguagem, use o [dev.java/learn](https://dev.java/learn/).

---

## 2. Fundamentos

### 2.1 Sintaxe Básica de Java

O mínimo para escrever o menu em terminal do Mini Projeto 1:

```java
import java.util.Scanner;

public class Main {
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        int opcao;

        do {
            System.out.println("1 - Cadastrar | 0 - Sair");
            opcao = Integer.parseInt(scanner.nextLine());

            switch (opcao) {
                case 1 -> System.out.println("Cadastrando...");
                case 0 -> System.out.println("Saindo...");
                default -> System.out.println("Opção inválida");
            }
        } while (opcao != 0);
    }
}
```

Pontos para dominar: tipos primitivos (`int`, `double`, `boolean`) vs. tipos de referência (`String`, objetos), `if/else`, `switch`, `for`, `while`, `do/while`, leitura com `Scanner` e conversão com `Integer.parseInt`.

!!! tip "Armadilha comum com `Scanner`"
    Misturar `nextInt()` com `nextLine()` deixa um `\n` no buffer e faz o `nextLine()` seguinte "pular". Por isso o exemplo acima lê sempre com `nextLine()` e converte depois.

### 2.2 Classes e Objetos

Toda classe define a estrutura (atributos) e o comportamento (métodos) de um objeto. O **construtor** é o método especial que inicializa os atributos quando o objeto é criado.

```java
public class Usuario {
    private Long id;
    private String nome;

    public Usuario(Long id, String nome) {
        this.id = id;
        this.nome = nome;
    }
}

Usuario u = new Usuario(1L, "João");
```

- Vídeos: Classes, Objetos e Construtores em Java
  - [O que é um Objeto](https://youtu.be/aR7CKNFECx0?si=QtGSP1-JSBU6ZENN)
  - [Criando Classes e Objetos](https://youtu.be/wNaoX6VOj54?si=X_wyz24o2zrsYsXG)

### 2.3 Enums

Um **enum** representa um conjunto fixo de valores. É o tipo certo para a prioridade de uma tarefa (e não uma `String` solta, que aceita qualquer texto).

```java
public enum Prioridade {
    BAIXA(1), MEDIA(2), ALTA(3);

    private final int peso;

    Prioridade(int peso) { this.peso = peso; }

    public int getPeso() { return peso; }
}

Prioridade p = Prioridade.ALTA;
System.out.println(p.getPeso());       // 3
System.out.println(p.name());          // "ALTA"
Prioridade lida = Prioridade.valueOf("MEDIA");   // lança IllegalArgumentException se o texto não existir

switch (p) {
    case ALTA -> System.out.println("Urgente");
    case MEDIA, BAIXA -> System.out.println("Normal");
}
```

Um enum pode ter atributos, construtor e métodos, como acima. `values()` devolve todos os valores. Mais adiante (Unidade VII) você verá por que gravar enums no banco pelo **nome** e não pela posição.

---

## 3. Os 4 Pilares da POO

### 3.1 Encapsulamento

Consiste em proteger os atributos tornando-os privados (`private`) e controlando o acesso por métodos públicos (`getters` e `setters`). O objetivo não é "ter getter e setter para tudo": é impedir que o objeto fique em um estado inválido. Se um atributo não deve mudar, não crie setter; se há regra de validação, ela fica no método que altera o estado.

```java
public void concluir() {
    if (this.concluida) {
        throw new IllegalStateException("Tarefa já concluída");
    }
    this.concluida = true;
}
```

- Vídeos: Encapsulamento em Java (get/set, modificadores de acesso)
  - [Visibilidade de um Objeto](https://youtu.be/jFI-qqitzwk?si=xfJrnGxzr0x4d4sE)
  - [Configurando Visibilidade de Atributos e Métodos](https://youtu.be/LV2243j4RTQ?si=eOC_LPkOMHfmCFX9)
  - [Métodos Especiais: Construtores, Getters, Setters](https://youtu.be/g2x9oyBFSco?si=l4kriaEHdxXKjeq_)
  - [Aplicação dos Métodos Especiais](https://youtu.be/6i-_R5cAcEc?si=eFrZDNAobFV7zykJ)
  - [Encapsulamento](https://youtu.be/1wYRGFXpVlg?si=yW0N3s9eCFgEMaji)
  - [Encapsulamento em Java](https://youtu.be/x4JfzV0Wb5w?si=OsMT9SB7hbl_nt6E)

### 3.2 Herança

Permite que uma classe (subclasse) reaproveite atributos e métodos de outra (superclasse) com `extends`. Serve para relações do tipo "é um": uma `TarefaComPrazo` **é uma** `Tarefa`.

```java
public class Tarefa {
    protected String titulo;
    public Tarefa(String titulo) { this.titulo = titulo; }
}

public class TarefaComPrazo extends Tarefa {
    private LocalDate prazo;

    public TarefaComPrazo(String titulo, LocalDate prazo) {
        super(titulo);          // chama o construtor da superclasse
        this.prazo = prazo;
    }
}
```

!!! warning "Herança não é reaproveitamento a qualquer custo"
    Use herança só quando a relação "é um" for verdadeira. Se for "tem um" (um `Usuario` **tem** `Tarefas`), use **composição**: um atributo do tipo da outra classe. Herança profunda deixa o código frágil.

- Vídeos: Herança em Java (extends, super, classes abstratas)
  - [Relacionamento Entre Classes](https://youtu.be/GLHbxDU9iBA?si=IndxRXouNmIgb3io)
  - [Objetos Compostos](https://youtu.be/BfrbCQ3XcrA?si=pK_kRay6KiCRJ_AF)
  - [Relação de Agregação](https://youtu.be/ERdvijGtrq0?si=SB7w-is_-BB0Z0im)
  - [Herança (parte 1)](https://youtu.be/_PZldwo0vVo?si=WLzRCIxOpXDhl20m)
  - [Herança (parte 2)](https://youtu.be/19IGAeoFKlU?si=ZqMrFuq3E3QRF4sl)
  - [Herança (parte 3)](https://youtu.be/He887D2WGVw?si=76U7yfUv6y9p1bh-)
  - [Herança (parte 4)](https://youtu.be/5pwV2WdD-_Y?si=Pf2PDPbF0LMQZ80g)

### 3.3 Polimorfismo

Permite que o mesmo código se comporte de formas diferentes dependendo do objeto real. Acontece de duas formas:

- **Sobrescrita** (`@Override`): a subclasse redefine um método da superclasse.
- **Sobrecarga**: métodos com o mesmo nome e parâmetros diferentes na mesma classe.

O núcleo do polimorfismo é o **upcasting com despacho dinâmico**: uma variável do tipo da superclasse pode guardar um objeto da subclasse, e o método executado é o do **objeto real**, decidido em tempo de execução.

```java
public class Tarefa {
    public String resumo() { return titulo; }
}

public class TarefaComPrazo extends Tarefa {
    @Override
    public String resumo() { return titulo + " (prazo: " + prazo + ")"; }
}

List<Tarefa> tarefas = new ArrayList<>();
tarefas.add(new Tarefa("Estudar"));
tarefas.add(new TarefaComPrazo("Entregar projeto", LocalDate.of(2026, 12, 1)));

for (Tarefa t : tarefas) {
    System.out.println(t.resumo());   // cada objeto executa a SUA versão de resumo()
}
```

É assim que `Tarefa` e `TarefaComPrazo` convivem na mesma lista. Quando você precisar acessar algo que só existe na subclasse, use `instanceof` com *pattern matching* (Java 16+):

```java
if (t instanceof TarefaComPrazo comPrazo) {
    System.out.println(comPrazo.getPrazo());
}
```

!!! tip "Prefira polimorfismo a `instanceof`"
    Se você está enchendo o código de `instanceof`, geralmente falta um método na superclasse que cada subclasse sobrescreva. Use `instanceof` quando realmente não houver alternativa.

- Vídeos: Polimorfismo em Java (sobrecarga e sobrescrita)
  - [O que é Polimorfismo](https://youtu.be/9-3-RMEMcq4?si=Hp_cZftE-4fDdUfS)
  - [Polimorfismo em Java](https://youtu.be/NctjqlfKC0U?si=tWtCn1cdXIPspkEE)
  - [Polimorfismo de Sobrecarga](https://youtu.be/hYek1xqWzgs?si=TspNufSJvVJCTkTJ)
  - [Aplicação do Polimorfismo por Sobrecarga](https://youtu.be/b7xGYh3NHZU?si=rtRBqap3h3Q-5F9u)

### 3.4 Abstração

Foca em expor só o essencial e esconder detalhes. Em Java, usa-se **classes abstratas** (`abstract`) e **interfaces**, que definem "o quê" uma classe deve fazer sem dizer "como".

```java
public interface Notificavel {
    void notificar(String mensagem);
}
```

Regra prática: **interface** descreve uma capacidade (o que algo sabe fazer); **classe abstrata** compartilha estado e código entre classes relacionadas. Uma classe herda de uma só superclasse, mas pode implementar várias interfaces.

- Vídeos: Abstração em Java (classes abstratas e interfaces)
  - [Classes Abstratas](https://youtu.be/ws1NVBGeegs?si=bKLtWxOUWtw4Ngk7)
  - [Métodos Abstratos](https://youtu.be/j97OEyzBcKI?si=M_oQkC4VkCYwpyNX)
  - [Métodos Abstratos: Regras](https://youtu.be/xI0xspht6mA?si=uzCPySAoH1cP47Ev)
  - [Introdução à Interfaces](https://youtu.be/AhVd_DzV3DU?si=MsJunQJ6V0pcmbSJ)
  - [Implementando Múltiplas Interfaces](https://youtu.be/QKjFkaagGdk?si=7ocDnsxGuk9cUnnK)

---

## 4. Tópicos Complementares (obrigatórios para as próximas unidades)

### 4.1 Membros `static`

Pertencem à **classe**, não a cada objeto: um atributo `static` é compartilhado, e um método `static` é chamado sem criar instância (`Math.max(1, 2)`).

- [Modificador `static`](https://youtu.be/WBBbsEdzzmA?si=h8nyBbEC0DNipjW7)
- [Métodos Estáticos](https://youtu.be/jowlUssbJmk?si=KFPoy1j8zAED4RE0)
- [Atributos e Métodos Estáticos](https://youtu.be/SYyEyR78dSQ?si=DfbTwZIVl2WVXU_D)
- [Bloco de Inicialização Estático](https://youtu.be/4YE1ewRK-rk?si=f-KXfU6HeHBm-sjy)

### 4.2 `toString`, `equals` e `hashCode`

Toda classe herda esses três métodos de `Object`, e o comportamento padrão provavelmente não é o que você quer:

- `toString()`: o padrão imprime algo como `Usuario@1b6d3586`. Sobrescreva para imprimir algo legível.
- `equals(Object o)`: o padrão compara **referência** (`a == b`: é exatamente o mesmo objeto na memória?). Sobrescreva quando dois objetos com os mesmos dados devam ser considerados iguais.
- `hashCode()`: devolve um número usado por coleções baseadas em tabela hash.

**O contrato:** se dois objetos são `equals`, eles **obrigatoriamente** devem ter o mesmo `hashCode`. Quem sobrescreve um deve sobrescrever o outro.

```java
@Override
public boolean equals(Object o) {
    if (this == o) return true;
    if (!(o instanceof Usuario outro)) return false;
    return Objects.equals(email, outro.email);
}

@Override
public int hashCode() {
    return Objects.hash(email);
}
```

Isso será crítico na Unidade III (`HashSet` e `HashMap` usam `equals`/`hashCode` para decidir se um elemento já existe) e na Unidade VII (entidades JPA).

### 4.3 Exceções

Uma **exceção** interrompe o fluxo normal quando algo dá errado. Em Java há duas famílias:

- **Checked** (herdam de `Exception`, exceto `RuntimeException`): o compilador **obriga** você a tratar com `try/catch` ou declarar com `throws`. Ex.: `IOException`.
- **Unchecked** (herdam de `RuntimeException`): não são exigidas pelo compilador. Ex.: `NullPointerException`, `IllegalArgumentException`.

```java
try {
    int n = Integer.parseInt(entrada);
    System.out.println(100 / n);
} catch (NumberFormatException e) {
    System.out.println("Digite um número válido");
} catch (ArithmeticException e) {
    System.out.println("Divisão por zero");
} finally {
    System.out.println("Executa sempre, com ou sem erro");
}
```

**Lançando e criando exceções próprias:** exceções de regra de negócio costumam ser *unchecked*, com nome que descreve o problema:

```java
public class TarefaNaoEncontradaException extends RuntimeException {
    public TarefaNaoEncontradaException(Long id) {
        super("Tarefa não encontrada: id " + id);
    }
}

// uso
throw new TarefaNaoEncontradaException(42L);
```

Esse padrão é a base do tratamento de erros em APIs (Unidade VIII): o serviço lança a exceção e um ponto central a traduz para `404`.

!!! warning "Não engula exceções"
    `catch (Exception e) { }` vazio esconde bugs. Ou trate de verdade, ou deixe propagar.

- Material oficial: [Exceptions no dev.java](https://dev.java/learn/exceptions/)

---

## 5. Checklist de Conclusão

- [ ] Escrevo um programa de terminal com menu em loop usando `Scanner`.
- [ ] Crio uma classe com atributos privados, construtor e getters, e explico por que não criei setter para tudo.
- [ ] Declaro um enum com atributo e uso em um `switch`.
- [ ] Explico a diferença entre herança ("é um") e composição ("tem um") com um exemplo.
- [ ] Mostro um `List<Tarefa>` contendo `Tarefa` e `TarefaComPrazo`, cada uma executando seu `resumo()`.
- [ ] Sobrescrevo `equals`, `hashCode` e `toString` e explico o contrato entre os dois primeiros.
- [ ] Crio uma exceção própria, lanço e trato com `try/catch`.

---

## 6. Projeto Prático de Consolidação

- 🌐 [Instruções do Mini Projeto 1: POO e Terminal ➔](desafio.md#mini-projeto-1)
