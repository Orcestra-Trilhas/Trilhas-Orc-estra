# Unidade VI – Spring Boot & APIs REST

---

## 1. Visão Geral

Agora que você já domina Java, banco de dados e o protocolo HTTP, é hora de integrar tudo construindo sua primeira **API REST** profissional com **Spring Boot**.

Nesta unidade, você entenderá os conceitos centrais do Spring Framework (Inversão de Controle, Injeção de Dependência, Beans e Contexto), como o Spring Boot simplifica o uso desses conceitos, e vai estruturar um projeto em camadas usando o padrão **MVC**, com ORM via **Spring Data JPA** e persistência em memória com **H2 Database**.

---

## 2. Fundamentos do Spring Framework

O **Spring Framework** é, na essência, um framework de **injeção de dependência**: ele existe para gerenciar a criação e o ciclo de vida dos objetos da sua aplicação, para que você não precise fazer isso manualmente com `new` espalhado pelo código.

### 6.1 Inversão de Controle (IoC)

Tradicionalmente, é a sua classe quem decide *quando* e *como* criar os objetos de que depende (ex.: um `TarefaService` fazendo `new TarefaRepository()` dentro dele mesmo). Com **Inversão de Controle**, essa responsabilidade é invertida: quem cria e gerencia os objetos passa a ser o **framework**, não a sua classe. Sua classe apenas *declara* do que precisa, e o Spring se encarrega de fornecer.

### 6.2 Injeção de Dependência (DI)

É o **mecanismo** pelo qual a Inversão de Controle acontece na prática: o Spring "injeta" as dependências que uma classe precisa, em vez dela criá-las sozinha. As formas mais comuns são:

- **Injeção via construtor** (recomendada): as dependências são passadas como parâmetros do construtor.
- **Injeção via campo** (`@Autowired` direto no atributo): mais simples de escrever, porém menos recomendada (dificulta testes e deixa dependências implícitas).

### 6.3 Beans

Um **Bean** é qualquer objeto cujo ciclo de vida (criação, configuração e destruição) é **gerenciado pelo Spring**, em vez de gerenciado manualmente por você. Uma classe se torna candidata a Bean ao ser anotada com estereótipos como `@Component`, `@Service`, `@Repository` ou `@Controller`, ou ao ser declarada manualmente em uma classe de configuração com `@Bean`.

### 6.4 Componentes (Component Scanning)

O Spring localiza automaticamente as classes que devem virar Beans através do **Component Scanning** — ele varre os pacotes do projeto em busca de classes anotadas com `@Component` (e suas especializações `@Service`, `@Repository`, `@Controller`), registrando-as automaticamente sem precisar de configuração manual em XML.

### 6.5 Contexto de Aplicação (ApplicationContext)

O **`ApplicationContext`** é o "container" central do Spring: é ele quem efetivamente instancia, configura e armazena todos os Beans da aplicação, e é dele que os Beans são retirados (injetados) sempre que uma classe declara precisar de uma dependência. Pode-se pensar nele como o registro vivo de tudo que o Spring está gerenciando durante a execução da aplicação.

---

## 3. Spring Boot: Simplificando o Spring

O **Spring Boot** não é um framework diferente do Spring — é uma camada construída **em cima** do Spring Framework, com foco em reduzir configuração manual e acelerar o início de um projeto. Os conceitos de IoC, DI, Beans e Contexto continuam sendo exatamente os mesmos da seção 2 — o Spring Boot só automatiza a forma de configurá-los, através dos três pilares abaixo.

### 6.6 Starters

Um **Starter** é um "pacote de dependências" pré-configurado para um propósito específico — em vez de você descobrir manualmente quais bibliotecas são compatíveis entre si para, por exemplo, construir uma API web, você adiciona uma única dependência (ex.: `spring-boot-starter-web`) e ela já traz tudo que é necessário (Spring MVC, Jackson para JSON, Tomcat embutido, etc.), com versões testadas e compatíveis entre si.

Alguns starters comuns:
- **`spring-boot-starter-web`**: para construir APIs REST e aplicações web (traz Spring MVC + servidor embutido).
- **`spring-boot-starter-data-jpa`**: para persistência com JPA/Hibernate.
- **`spring-boot-starter-security`**: para autenticação e autorização com Spring Security.
- **`spring-boot-starter-test`**: para testes (JUnit, Mockito, etc.).

Isso resolve um problema real do Spring "clássico": antes dos starters, era comum ter conflitos de versão entre bibliotecas (o famoso "dependency hell"), já que cada uma precisava ser adicionada e versionada manualmente.

### 6.7 Auto-Configuração (Auto-Configuration)

É o mecanismo pelo qual o Spring Boot **configura Beans automaticamente**, com base no que ele encontra no classpath do seu projeto. Funciona assim, na prática:

1. Ao subir a aplicação, o Spring Boot analisa quais dependências (jars) estão presentes no projeto.
2. Para cada uma que ele reconhece, existe uma classe de auto-configuração correspondente (anotada internamente com `@Conditional...`), que só é ativada **se** determinadas condições forem atendidas — ex.: "se existir um driver do H2 no classpath **e** não houver um Bean de `DataSource` já definido manualmente, crie um `DataSource` automaticamente apontando para um banco H2 em memória".
3. Isso significa que a auto-configuração **nunca sobrescreve** uma configuração que você já fez manualmente — ela só entra em ação para preencher o que está faltando, seguindo um princípio de "configuração sensata por padrão, mas sempre sobrescrevível".

Na prática, é a auto-configuração que permite você simplesmente adicionar `spring-boot-starter-data-jpa` + o driver do H2 e já ter um banco funcionando, sem escrever uma linha de configuração de `DataSource`.

### 6.8 Servidores Embutidos (Embedded Servers)

Tradicionalmente, uma aplicação Java web precisava ser empacotada como `.war` e implantada manualmente em um servidor externo (como um Tomcat instalado à parte na máquina/servidor). O Spring Boot inverte essa lógica: o servidor web (por padrão, o **Tomcat**, mas também é possível usar **Jetty** ou **Undertow**) vem **embutido dentro do próprio `.jar`** da aplicação.

Isso significa que rodar a aplicação é literalmente executar `java -jar aplicacao.jar` — o servidor sobe junto, na mesma JVM, sem exigir instalação ou configuração externa. É esse mecanismo que permite testar a API localmente (ex.: em `localhost:8080`) assim que você inicia o projeto, sem nenhum passo adicional de deploy.

---

## 4. Estrutura em Camadas (MVC)

Com esses conceitos entendidos, o projeto será organizado seguindo o padrão **MVC** adaptado para APIs REST:

- **Controller**: camada responsável por receber as requisições HTTP e devolver as respostas — não contém regra de negócio.
- **Service**: camada onde fica a regra de negócio da aplicação.
- **Repository**: camada responsável pela comunicação com o banco de dados, usando o **Spring Data JPA** para abstrair as operações de persistência (ORM).

---

## 5. Artigos

- 🌐 [Artigo: Como conectar o H2 Database com o Spring Boot](https://wpsilva.medium.com/utilizando-banco-de-dados-h2-com-spring-de-forma-r%C3%A1pida-e-simples-6d896e15a4af)

---

## 6. Projeto Prático: API REST do Gerenciador de Tarefas

Transforme sua aplicação de terminal em uma API REST profissional com Spring Boot e H2:

- 🌐 [Instruções do Mini Projeto Spring Boot ➔](desafio.md#3-mini-projeto-3-api-rest-com-spring-boot-h2-database)
