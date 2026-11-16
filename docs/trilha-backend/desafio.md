# Central de Projetos e Desafios: Back-end

---

Esta página reúne os projetos práticos da Trilha de Back-end. Eles formam **um único sistema que evolui**: o **Gerenciador de Tarefas** nasce como um programa de terminal e termina como uma API REST segura, com banco de dados, testes e documentação.

Cada projeto usa **somente o que as unidades anteriores ensinaram**. Se um projeto exigir algo que você não viu, avise: é falha da trilha, não sua.

---

## 0. Convenções Gerais

### 0.1 Domínio de Referência

Todos os projetos usam o mesmo domínio e os mesmos nomes, **em português**:

| Entidade | Atributos |
| :--- | :--- |
| `Usuario` | `id`, `nome`, `email` (único), `tarefas` |
| `Perfil` (1:1 com `Usuario`) | `id`, `bio`, `telefone` |
| `Tarefa` | `id`, `titulo` (obrigatório), `descricao`, `concluida`, `usuario` |
| `TarefaComPrazo` (herda de `Tarefa`) | `prazo` (`LocalDate`), `prioridade` (`Prioridade`) |
| `Tag` (N:N com `Tarefa`) | `id`, `nome` (único) |
| `Prioridade` (enum) | `BAIXA`, `MEDIA`, `ALTA` |

Rotas da API usam o plural em português: `/usuarios`, `/tarefas`, `/tags`. **Não** misture `Task`/`Tarefa` nem `titulo`/`title`.

### 0.2 Dependências Permitidas

- **Lombok é opcional.** Se usar, siga os cuidados da Unidade VII (nada de `@Data` em entidades).
- **DTOs são Records** (Unidade III).
- Entidades são classes comuns.

### 0.3 Estrutura do Repositório e Entrega

Cada membro tem **um repositório individual** com uma pasta para cada área de capacitação. Cada membro faz uma capacitação por semestre, na área que escolher, e **todo o desenvolvimento e a conclusão dos desafios da trilha de Back-end ficam dentro da pasta `backend/`** do próprio repositório. Não altere as pastas das outras áreas.

> Estes desafios são **exclusivos da trilha** (aprendizagem dos conceitos das unidades). Eles não têm relação com os desafios semanais e **não** vão para o repositório dos desafios semanais.

#### Estrutura padrão do repositório

```text
repositorio-do-membro/
├── README.md                          # apresentação e progresso geral
├── .gitignore                         # único, na raiz
├── .github/
│   └── pull_request_template.md       # modelo de Pull Request
├── backend/                           # <- tudo da trilha de Back-end fica aqui
│   ├── README.md                      # tabela de progresso da trilha
│   ├── 00-atividade-ambiente/
│   ├── 01-mini-projeto-poo-terminal/
│   ├── 02-mini-projeto-modelagem-bd/
│   ├── 03-atividade-http-postman/
│   ├── 04-mini-projeto-spring-boot/
│   ├── 05-mini-projeto-spring-data-jpa/
│   ├── 06-mini-projeto-spring-web/
│   └── 07-projeto-final-spring-security/
├── design/
├── devops/
├── front/
├── gamificacao/
└── mobile/
```

As pastas das áreas em que você não está se capacitando no semestre ficam vazias (use um arquivo `.gitkeep`).

#### Mapa: desafio → pasta de entrega

| Desafio | Pasta de entrega (dentro de `backend/`) | Tipo |
| :--- | :--- | :--- |
| Atividade 0: Ambiente | `00-atividade-ambiente/` | projeto Maven |
| Mini Projeto 1: POO e Terminal | `01-mini-projeto-poo-terminal/` | projeto Maven (sem Spring) |
| Mini Projeto 2: Modelagem do BD | `02-mini-projeto-modelagem-bd/` | documentos e SQL |
| Atividade 3: Postman e HTTP | `03-atividade-http-postman/` | coleção e README |
| Mini Projeto 3: Spring Boot | `04-mini-projeto-spring-boot/` | projeto Spring Boot |
| Mini Projeto 4: Spring Data JPA | `05-mini-projeto-spring-data-jpa/` | projeto Spring Boot |
| Mini Projeto 5: Spring Web | `06-mini-projeto-spring-web/` | projeto Spring Boot |
| Projeto Final: Spring Security | `07-projeto-final-spring-security/` | projeto Spring Boot |

O prefixo numérico da pasta segue a **ordem de execução** da trilha e é diferente do número do desafio (por exemplo, o Mini Projeto 3 fica em `04-...`). Use **exatamente** estes nomes.

#### Regras de nomenclatura

- Pastas e arquivos em **minúsculas, sem acentos, sem espaços**, separados por hífen (`kebab-case`). Exceções: `README.md`, `pom.xml` e arquivos gerados pelo Maven/Spring.
- Pacote Java base: `com.orcestra.tarefas`. `groupId` Maven: `com.orcestra`.
- **Cada pasta de projeto é autocontida**: o `pom.xml` fica na raiz da pasta do projeto (não na raiz do repositório). Abra **a pasta do projeto** na IDE, não o repositório inteiro.

#### Estrutura interna de cada projeto

```text
backend/05-mini-projeto-spring-data-jpa/
├── README.md                  # obrigatório (modelo abaixo)
├── pom.xml
├── mvnw, mvnw.cmd, .mvn/      # Maven Wrapper (projetos Spring Boot)
├── src/
│   ├── main/
│   │   ├── java/com/orcestra/tarefas/...
│   │   └── resources/         # application.properties
│   └── test/
│       └── java/com/orcestra/tarefas/...
├── docs/                      # prints, diagramas, relatórios de diagnóstico
├── sql/                       # scripts SQL (quando houver)
└── postman/                   # coleções exportadas (quando houver)
```

`docs/`, `sql/` e `postman/` só existem nos projetos que têm esses artefatos. `src/test/java` fica sempre presente, mesmo vazia (use `.gitkeep`).

#### Onde entregar cada artefato

| Projeto | Artefatos e local |
| :--- | :--- |
| 00 | projeto Maven na raiz da pasta; `docs/java-version.png` e `docs/tomcat-localhost.png` |
| 01 | projeto Maven na raiz da pasta; `README.md` |
| 02 | `docs/der-conceitual.png` (ou `.pdf`); `docs/modelo-logico.md`; `sql/schema.sql`; `sql/consultas.sql`; `README.md` com a justificativa da herança e da 3FN |
| 03 | `postman/colecao-jsonplaceholder.json`; `README.md` com o **link** do vídeo e as respostas sobre `POST` e CORS |
| 04 | projeto Spring Boot; testes em `src/test/java`; `README.md` |
| 05 | projeto Spring Boot; `docs/diagnostico-jpa.md` (logs do N+1 antes/depois e a `LazyInitializationException`); `README.md` |
| 06 | projeto Spring Boot; `postman/colecao-api-tarefas.json`; `README.md` |
| 07 | projeto Spring Boot; `postman/colecao-api-seguranca.json`; `docs/fluxo-seguranca.md`; `README.md` com as decisões de segurança |

**Vídeos não vão para o Git.** Suba em um serviço (YouTube não listado, Drive) e coloque o link no `README.md` do projeto. Em geral, nenhum arquivo acima de 10 MB entra no repositório.

#### Evolução entre projetos (04 → 05 → 06 → 07)

Os projetos 05, 06 e 07 **continuam** o projeto anterior. Para manter cada entrega revisável e independente:

1. Com o projeto anterior **aprovado e já mesclado** na `main`, **copie a pasta** dele para a nova (sem `target/`). Ex.: copie `04-mini-projeto-spring-boot/` para `05-mini-projeto-spring-data-jpa/`.
2. Continue o desenvolvimento **apenas na pasta nova**.
3. **Não altere** a pasta do projeto anterior depois de aprovada: ela é o registro daquela etapa.

O projeto 04 é um projeto Spring novo (Spring Initializr), não uma cópia do 01; você pode reaproveitar as classes de modelo do 01.

#### Fluxo de Git (repita para cada projeto)

Uma branch e um Pull Request por projeto, **um projeto por vez**.

```bash
# 1. Parta da main atualizada
git checkout main
git pull

# 2. Crie a branch do projeto (backend/<nome-da-pasta>)
git checkout -b backend/01-mini-projeto-poo-terminal

# 3. Crie a pasta, desenvolva e faça commits pequenos
mkdir -p backend/01-mini-projeto-poo-terminal
git add backend/01-mini-projeto-poo-terminal
git commit -m "Adiciona classes de modelo e enum Prioridade"

# 4. Envie a branch
git push -u origin backend/01-mini-projeto-poo-terminal
```

5. No GitHub, abra um **Pull Request** da sua branch para a `main` **do seu próprio repositório**.
   - Título: `[Backend] Mini Projeto 1: POO e Terminal` (use o nome do desafio).
   - Preencha o modelo do PR e peça a revisão do **assessor de conhecimentos de Back-end**.
6. Se houver ajustes, faça novos commits **na mesma branch** e envie (`git push`); o PR se atualiza sozinho.
7. Antes do merge, marque o projeto como concluído na tabela de `backend/README.md` (dentro do próprio PR).
8. Com o PR **aprovado**, faça o merge na `main` e só então comece o próximo projeto, voltando ao passo 1.

**Mensagens de commit:** em português, no imperativo, descrevendo uma mudança ("Adiciona busca por título", "Corrige validação de e-mail"). Evite "ajustes", "final", "v2".

#### O que nunca vai para o repositório

Controlado pelo `.gitignore` da raiz: `target/`, `.idea/`, `.vscode/`, `*.iml`, arquivos `.env`, `application-local.properties`, `*.log`, `.DS_Store`.

- **Nenhum segredo** (senhas, chaves JWT, tokens) em arquivo versionado. Documente no README **o nome** da variável de ambiente (ex.: `JWT_SECRET`), nunca o valor.
- Se um segredo for commitado por engano, **troque-o** (apagar o arquivo em outro commit não remove do histórico).

#### Modelo de `README.md` de projeto

Cada pasta de projeto tem um `README.md` com:

```markdown
# <Nome do desafio>

## Descrição
Uma ou duas frases sobre o que o projeto faz.

## Como executar
Comandos exatos (ex.: `./mvnw spring-boot:run`) e variáveis de ambiente necessárias (apenas os nomes).

## Como testar
Comando dos testes (ex.: `./mvnw test`) e onde está a coleção do Postman, se houver.

## Decisões de design
Respostas às perguntas pedidas no desafio (veja "README" em cada projeto).

## Links
Vídeo (se houver) e documentação adicional.
```

Os arquivos-modelo (`README.md` da raiz, `.gitignore`, `.github/pull_request_template.md` e `backend/README.md`) estão prontos para copiar para o repositório do membro.

### 0.4 Critérios de Avaliação (valem para todos os projetos)

| Critério | O que se observa |
| :--- | :--- |
| **Funcionamento** | cumpre os critérios de aceite do projeto, sem erro ao executar |
| **Qualidade do código** | nomes claros, classes coesas, sem código duplicado, camadas respeitadas |
| **Testes** | exigidos a partir do Mini Projeto 3, conforme cada critério de aceite |
| **README e decisões** | explica como rodar e justifica as escolhas pedidas |
| **Git** | histórico legível, commits pequenos, sem arquivos gerados (`target/`) |

Um projeto é considerado **aprovado** quando atende **todos** os critérios de aceite marcados como obrigatórios.

---

## Atividade 0: Ambiente { #atividade-0 }

**Unidade I.**

**Pasta de entrega:** `backend/00-atividade-ambiente/` (veja a seção 0.3)

### Objetivo
Validar o ambiente de desenvolvimento.

### Tarefa
1. Gere um projeto no [Spring Initializr](https://start.spring.io) (Maven, Java 21, Spring Boot 3.x, dependência *Spring Web*).
2. Abra na IDE e suba a aplicação com `./mvnw spring-boot:run`.
3. Acesse `http://localhost:8080` e tire um print do resultado.
4. Rode `./mvnw test` e confirme que o teste padrão passa.

### Critérios de Aceite
- Print de `java -version` mostrando a versão 21.
- Print do Tomcat iniciado e da requisição ao `localhost:8080`.
- `./mvnw test` passando.

---

## 1. Mini Projeto 1: POO e Terminal (Gerenciador de Tarefas) { #mini-projeto-1 }

**Unidades II e III.**

**Pasta de entrega:** `backend/01-mini-projeto-poo-terminal/` (veja a seção 0.3)

### Objetivo
Praticar POO (classes, herança, polimorfismo, enums, exceções) e, na Parte 2, Collections, Stream API, Optional, Records e `java.time`, **somente com Java e terminal**.

> **Importante:** não use banco de dados, interface gráfica nem frameworks nesta etapa. Tudo funciona pelo terminal, com os dados em memória. O **Maven** é obrigatório, mas é só a ferramenta de build (não é um framework de aplicação): o projeto segue a estrutura padrão de projeto Maven.

### Parte 1: Requisitos Básicos de POO

#### Classes

| Classe | Atributos / regras |
| :--- | :--- |
| `Usuario` | `id`, `nome`, `email`, `tarefas` (lista de `Tarefa`). `equals`/`hashCode` por `email`. |
| `Tarefa` | `id`, `titulo`, `descricao`, `concluida`. Método `concluir()` que lança exceção se já concluída. Método `resumo()`. |
| `TarefaComPrazo` | **herda de `Tarefa`**. Acrescenta `prazo` (`LocalDate`) e `prioridade` (`Prioridade`). Sobrescreve `resumo()`. |
| `Prioridade` (enum) | `BAIXA`, `MEDIA`, `ALTA`, cada uma com um `peso` (1, 2, 3). |

#### Exceções
Crie pelo menos `UsuarioNaoEncontradoException` e `TarefaNaoEncontradaException` (unchecked). Entradas inválidas do menu **não podem derrubar o programa**: trate com `try/catch`.

#### Estrutura (projeto Maven, sem Spring)

Crie um projeto **Maven** simples na IDE (IntelliJ: *New Project → Maven*; VS Code: extensão *Maven for Java*), dentro da pasta de entrega. Não use o Spring Initializr aqui.

```text
backend/01-mini-projeto-poo-terminal/
├── pom.xml
├── README.md
└── src/
    ├── main/java/com/orcestra/tarefas/
    │   ├── Main.java
    │   ├── model/
    │   │   ├── Usuario.java
    │   │   ├── Tarefa.java
    │   │   ├── TarefaComPrazo.java
    │   │   └── Prioridade.java
    │   ├── exception/
    │   │   ├── UsuarioNaoEncontradoException.java
    │   │   └── TarefaNaoEncontradaException.java
    │   └── service/
    │       └── GerenciadorService.java     // regras e listas em memória
    └── test/java/com/orcestra/tarefas/     // pode ficar vazio (.gitkeep)
```

`pom.xml` mínimo:

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.orcestra</groupId>
    <artifactId>gerenciador-tarefas-terminal</artifactId>
    <version>1.0.0</version>

    <properties>
        <maven.compiler.release>21</maven.compiler.release>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    </properties>
</project>
```

Execute pela IDE (rodando a classe `Main`) e documente no README o comando equivalente por terminal, por exemplo `mvn compile` seguido de `java -cp target/classes com.orcestra.tarefas.Main`.

#### Menu do Terminal
```text
=============================
 GERENCIADOR DE TAREFAS
=============================
1 - Cadastrar usuário
2 - Listar usuários
3 - Criar tarefa
4 - Criar tarefa com prazo
5 - Listar tarefas de um usuário
6 - Concluir tarefa
0 - Sair
```

### Parte 2: Estruturas de Dados e Manipulação de Listas { #mini-projeto-1-parte-2 }

Evolua o projeto aplicando a Unidade III:

- **Nível 1 (Edição e remoção):** editar e remover usuário; editar e remover tarefa. Ao remover um usuário, suas tarefas também saem.
- **Nível 2 (Buscas):** buscar usuário por nome (contém, sem diferenciar maiúsculas) e tarefa por título. Use `Optional` quando a busca é por identificador.
- **Nível 3 (Estatísticas):** total de usuários, total de tarefas, concluídas e pendentes, e tarefas por usuário (`Collectors.groupingBy`).
- **Nível 4 (Ordenação):** ordenar tarefas por título; ordenar as `TarefaComPrazo` por prazo e por prioridade (ordem decrescente de `peso`). Use `Stream` e `Comparator`. Como só `TarefaComPrazo` tem prazo/prioridade, **filtre com `instanceof` (pattern matching)** antes de ordenar.
- **Nível 5 (Tipos modernos):** use um **Record** `TarefaResumo` para exibir listagens, e o pacote `java.time` para marcar como **atrasadas** as tarefas com prazo anterior a hoje.

### Critérios de Aceite
**Obrigatórios:**
- Classes e enum conforme a tabela, com encapsulamento (atributos privados).
- Herança e polimorfismo demonstrados: uma `List<Tarefa>` com os dois tipos, cada um exibindo seu `resumo()`.
- `equals`/`hashCode`/`toString` implementados em `Usuario`.
- Menu funcional que não quebra com entrada inválida (letras, opção inexistente, id inexistente).
- Níveis 1 a 4 da Parte 2 implementados.
- Código organizado em pacotes.

**Desejáveis:** Nível 5.

**README:** explique por que `TarefaComPrazo` **é uma** `Tarefa` (herança) e `Usuario` **tem** `Tarefas` (composição).

---

## 2. Mini Projeto 2: Modelagem do Banco de Dados { #mini-projeto-2 }

**Unidade IV.**

**Pasta de entrega:** `backend/02-mini-projeto-modelagem-bd/` (veja a seção 0.3)

### Objetivo
Planejar o armazenamento do sistema, do modelo abstrato ao script SQL, e praticar SQL.

### Etapa 1: Modelagem Conceitual
Elabore um **DER** (pode ser desenhado em ferramenta como o draw.io ou o dbdiagram.io) com todas as entidades do domínio de referência (`Usuario`, `Perfil`, `Tarefa`, `TarefaComPrazo`, `Tag`), seus atributos e relacionamentos, indicando as cardinalidades:
- **1:1:** `Usuario` e `Perfil`.
- **1:N:** `Usuario` e `Tarefa`.
- **N:N:** `Tarefa` e `Tag`.
- **Especialização:** `TarefaComPrazo` como tipo de `Tarefa`.

### Etapa 2: Modelagem Lógica
Para cada entidade: nome da tabela, atributos com tipos, **PK**, **FK**, restrições (`NOT NULL`, `UNIQUE`), a **tabela associativa** do N:N e o mapeamento do **enum** `Prioridade`.

**Decisão de herança:** escolha uma das três estratégias da Unidade IV (tabela única, tabela por classe, tabela por classe concreta) e **justifique por escrito** (nas decisões do README). A trilha adota **tabela única** na implementação; se você escolher outra, justifique o porquê e o impacto.

### Etapa 3: Modelagem Física (H2)
Escreva o script `schema.sql` com os `CREATE TABLE` para o **H2** (tipos, constraints e ao menos um índice em FK).

### Etapa 4: Exercícios de SQL
Escreva em `consultas.sql` (testadas em um editor online, como o DB Fiddle, ou no console do H2 depois da Unidade VII):

1. Inserir 2 usuários e 5 tarefas (uma de cada tipo, com e sem prazo).
2. Listar as tarefas pendentes de um usuário, ordenadas por título.
3. Listar tarefas com o **nome do dono** (`INNER JOIN`).
4. Listar **todos os usuários**, inclusive os que não têm tarefas (`LEFT JOIN`).
5. Contar tarefas por usuário (`GROUP BY`), mostrando apenas quem tem mais de uma (`HAVING`).
6. Listar as tarefas de uma determinada `Tag` (JOIN na tabela associativa).
7. Marcar como concluídas todas as tarefas com prazo vencido (`UPDATE` com `WHERE`).

### Critérios de Aceite
**Obrigatórios:**
- DER completo com as quatro cardinalidades/especialização.
- Modelo lógico com PK, FK, tipos, restrições e a tabela associativa N:N.
- Justificativa da estratégia de herança.
- `schema.sql` executa **sem erro** no H2.
- As 7 consultas de `consultas.sql` executam e retornam o resultado esperado.
- O modelo está em **3FN** (indique em uma frase por que não há dependência transitiva).

**Importante:** o Mini Projeto 4 deve **seguir este modelo**. Divergências são decisões a justificar.

---

## Atividade 3: Coleção Postman e HTTP { #atividade-3 }

**Unidade V.**

**Pasta de entrega:** `backend/03-atividade-http-postman/` (veja a seção 0.3)

### Objetivo
Dominar a leitura e a construção de requisições HTTP antes de ter uma API própria.

### Tarefa
Crie uma coleção no **Postman** ou **Insomnia** contra a API pública [JSONPlaceholder](https://jsonplaceholder.typicode.com) com, no mínimo:
- Um `GET` de lista e um `GET` por id (path variable).
- Um `GET` com query parameter (ex.: `/posts?userId=1`).
- Um `POST` com body JSON.
- Um `PUT`, um `PATCH` e um `DELETE`.
- Um `GET` de id inexistente (observe o `404`).

Grave um vídeo curto (até 5 minutos) explicando, para cada requisição, o verbo, o status recebido e o que ele significa.

### Critérios de Aceite
**Obrigatórios:**
- Coleção exportada (`.json`) no repositório.
- Vídeo explicando verbo, status code e diferença entre `PUT` e `PATCH`.
- Um parágrafo no README: *por que `POST` não é idempotente e qual o risco?* e *o que é CORS?*

---

## 3. Mini Projeto 3: Esqueleto Spring Boot em Camadas { #mini-projeto-3 }

**Unidade VI.**

**Pasta de entrega:** `backend/04-mini-projeto-spring-boot/` (veja a seção 0.3)

### Objetivo
Montar o esqueleto da aplicação aplicando IoC, DI, camadas, configuração, logs e um primeiro teste, com repositório **em memória** (o banco chega no Mini Projeto 4).

### Requisitos

1. Projeto criado no Spring Initializr com **Spring Web** e **Spring Boot DevTools** (opcional). Não adicione JPA ainda.
2. Arquitetura em camadas, com **injeção por construtor** em todas as classes:

```text
src/main/java/com/orcestra/tarefas/
├── TarefasApplication.java
├── controller/
│   └── UsuarioController.java
├── service/
│   └── UsuarioService.java
├── repository/
│   └── UsuarioRepository.java       // @Repository com Map em memória
├── model/
│   ├── Usuario.java
│   ├── Tarefa.java
│   ├── TarefaComPrazo.java
│   └── Prioridade.java
└── exception/
    └── UsuarioNaoEncontradoException.java
```

3. `UsuarioRepository` com `save`, `findById` (devolve `Optional`), `findAll` e `deleteById`, usando os **mesmos nomes do Spring Data** (na Unidade VII você os substituirá por um `JpaRepository`).
4. `UsuarioService` com a lógica de listar, buscar por id (lança `UsuarioNaoEncontradoException` quando não existe) e cadastrar.
5. `UsuarioController` com `GET /usuarios` e `GET /usuarios/{id}` (**por ora podem devolver o modelo direto**; os DTOs e o tratamento de erros são a Unidade VIII).
6. `application.properties` com `spring.application.name` e `logging.level.com.orcestra=DEBUG`; **um valor lido de uma variável de ambiente** via `@Value` (ex.: um nome de ambiente).
7. Logs com SLF4J (`log.info`) no Service ao cadastrar e ao não encontrar.
8. Um **bean** declarado com `@Bean` em uma classe `@Configuration` (ex.: um `Clock`).

### Critérios de Aceite
**Obrigatórios:**
- `./mvnw spring-boot:run` sobe sem erro e `GET /usuarios` responde `200`.
- Camadas respeitadas (Controller não acessa Repository) e **nenhum `@Autowired` em campo**.
- Pelo menos **2 testes de unidade de Service** com Mockito (caminho feliz e usuário inexistente).
- Nenhum `System.out.println`; logs via SLF4J.
- Valor sensível/ambiental vindo de variável de ambiente (sem valor fixo no código).

**README:** explique, com suas palavras, o que o Spring faz com `@SpringBootApplication` e por que o `@Service` precisa estar abaixo do pacote principal.

---

## 4. Mini Projeto 4: Persistência com Spring Data JPA { #mini-projeto-4 }

**Unidade VII.**

**Pasta de entrega:** `backend/05-mini-projeto-spring-data-jpa/` (veja a seção 0.3)

### Objetivo
Trocar o repositório em memória por persistência real no **H2**, implementando o modelo do Mini Projeto 2 e **migrando as funcionalidades de busca, estatística e ordenação** do Mini Projeto 1 para consultas JPA.

### Requisitos

**Dependências:** adicione `Spring Data JPA` e `H2 Database`.

**Configuração:** `show-sql` e `format_sql` ligados; console do H2 habilitado (`spring.h2.console.enabled=true`); `spring.jpa.open-in-view=false`.

**Entidades** (seguindo o Mini Projeto 2):
- `Usuario`, `Perfil` (1:1), `Tarefa`, `TarefaComPrazo` (herança `SINGLE_TABLE`), `Tag` (N:N com `Tarefa`).
- Enum `Prioridade` com `@Enumerated(EnumType.STRING)`.
- Relação bidirecional `Usuario` / `Tarefa` com **`mappedBy`**, `cascade` e `orphanRemoval`, e método auxiliar `adicionarTarefa`.
- `@ManyToOne` com `FetchType.LAZY`.

**Repositórios:** `UsuarioRepository`, `TarefaRepository` e `TagRepository` como `JpaRepository`. O **Service do Mini Projeto 3 deve continuar funcionando sem mudança de lógica** (só ajustes mínimos).

**Consultas obrigatórias** (cada uma com a técnica indicada):

| Funcionalidade | Técnica |
| :--- | :--- |
| Buscar usuário por e-mail; verificar se e-mail já existe | Query Methods (`findByEmail`, `existsByEmail`) |
| Buscar tarefas por título (contém, sem diferenciar maiúsculas) | Query Method |
| Listar tarefas de um usuário ordenadas por título | Query Method |
| Listar pendentes de um usuário | `@Query` (JPQL) |
| Estatística: total, concluídas e pendentes por usuário | `@Query` (JPQL com `COUNT`/`GROUP BY`) |
| Listar tarefas paginadas (com `Pageable`) | `Page<Tarefa>` |
| Busca com **filtros opcionais** (concluída, título, prioridade) | `Specification` |
| Listar usuários com suas tarefas **sem N+1** | `@EntityGraph` ou `JOIN FETCH` |

**Transação:** um método de Service `@Transactional` que conclui uma tarefa **sem chamar `save()`** (dirty checking) e outro que cadastra um usuário com seu `Perfil` em uma só operação.

**Exercício de diagnóstico (obrigatório, vai em `docs/diagnostico-jpa.md`):**
1. Provoque um **N+1** listando usuários e acessando `tarefas`; cole no documento o trecho do log que o evidencia e a correção aplicada, com o log "depois".
2. Provoque uma **`LazyInitializationException`** (acessando uma relação `LAZY` fora da transação) e explique a correção.

### Critérios de Aceite
**Obrigatórios:**
- Todas as entidades mapeadas, com as relações 1:1, 1:N, N:N e a herança.
- Dados persistem entre as chamadas (e somem ao reiniciar, por ser H2 em memória); o console do H2 mostra as tabelas conforme o modelo do Mini Projeto 2.
- Todas as consultas da tabela implementadas com a técnica indicada.
- N+1 e `LazyInitializationException` demonstrados e corrigidos em `docs/diagnostico-jpa.md`.
- **Pelo menos 3 testes `@DataJpaTest`** (por exemplo, a `@Query` de pendentes, a paginação e a `Specification`).
- Nenhum `FetchType.EAGER` sem justificativa; nenhum `@Data` em entidade.

---

## 5. Mini Projeto 5: API REST com Spring Web { #mini-projeto-5 }

**Unidade VIII.**

**Pasta de entrega:** `backend/06-mini-projeto-spring-web/` (veja a seção 0.3)

### Objetivo
Expor a persistência como uma **API REST completa**, com DTOs, validação, erros padronizados, documentação e testes.

### Dependências
Adicione `Validation` e `springdoc-openapi` (Swagger).

### Estrutura
```text
src/main/java/com/orcestra/tarefas/
├── controller/    UsuarioController, TarefaController, TagController
├── service/
├── repository/
├── model/         (entidades)
├── dto/           UsuarioRequest/Response, TarefaRequest/Response, TagRequest/Response (Records)
└── exception/     RecursoNaoEncontradoException (base), ConflitoException, GlobalExceptionHandler
```

### Endpoints

#### Usuários (`/usuarios`)
| Método | Endpoint | Descrição | Sucesso |
| :--- | :--- | :--- | :---: |
| `GET` | `/usuarios` | Lista usuários (paginado) | `200` |
| `GET` | `/usuarios/{id}` | Busca por id | `200` |
| `POST` | `/usuarios` | Cadastra usuário | `201` + `Location` |
| `PUT` | `/usuarios/{id}` | Substitui os dados | `200` |
| `DELETE` | `/usuarios/{id}` | Remove usuário | `204` |
| `GET` | `/usuarios/{id}/tarefas` | Tarefas do usuário | `200` |

#### Tarefas (`/tarefas`)
| Método | Endpoint | Descrição | Sucesso |
| :--- | :--- | :--- | :---: |
| `GET` | `/tarefas` | Lista com filtros opcionais `concluida`, `titulo`, `prioridade` e paginação | `200` |
| `GET` | `/tarefas/{id}` | Busca por id | `200` |
| `POST` | `/tarefas` | Cria tarefa (comum ou com prazo, conforme os campos enviados) | `201` + `Location` |
| `PUT` | `/tarefas/{id}` | Substitui a tarefa | `200` |
| `PATCH` | `/tarefas/{id}/concluir` | Marca como concluída | `200` |
| `DELETE` | `/tarefas/{id}` | Remove tarefa | `204` |
| `GET` | `/tarefas/estatisticas` | Total, concluídas e pendentes | `200` |

#### Tags (`/tags`)
`GET /tags`, `POST /tags` (`201`), `DELETE /tags/{id}` (`204`) e `POST /tarefas/{id}/tags/{tagId}` para associar.

### Exemplos de Payload

**Criar usuário** (`POST /usuarios`)
```json
{ "nome": "João Silva", "email": "joao@orcestra.com" }
```

**Criar tarefa simples** (`POST /tarefas`)
```json
{ "titulo": "Implementar Controller", "descricao": "Criar endpoints REST", "usuarioId": 1 }
```

**Criar tarefa com prazo**
```json
{ "titulo": "Entregar projeto", "usuarioId": 1, "prazo": "2026-12-31", "prioridade": "ALTA" }
```

**Exemplo de erro de validação** (`400`)
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

### Regras
- **O Controller nunca recebe nem devolve entidade**: somente DTOs (Records).
- Validação: `titulo` obrigatório e até 150 caracteres; `email` válido e **único** (`409` se duplicado); `usuarioId` obrigatório e existente (`404` se não existir); `prazo` não pode estar no passado.
- **Todos os erros (400, 404, 409, 500) usam o mesmo formato `ProblemDetail`**, e o `500` nunca mostra stack trace.
- Nenhum Controller com `try/catch` de regra de negócio.
- CORS configurado para a origem do front-end (`http://localhost:3000`).
- Swagger acessível em `/swagger-ui.html`.

### Critérios de Aceite
**Obrigatórios:**
- Todos os endpoints das tabelas funcionando com os **status codes indicados**.
- DTOs de entrada e saída; sem recursão infinita no JSON; sem expor a entidade.
- `@Valid` com mensagens em português e respostas `400` no formato padrão.
- `@RestControllerAdvice` cobrindo 404, 409, 400 e o handler genérico `500`.
- **Coleção Postman** (a da Atividade 3, adaptada) cobrindo todos os endpoints, incluindo casos de erro, exportada no repositório.
- **Pelo menos 4 testes `@WebMvcTest`**: um `201` com `Location`, um `404`, um `400` de validação e um `409`.
- Testes de Service e de Repository anteriores continuam passando.

**README:** liste três motivos pelos quais a API não expõe entidades e explique a decisão do `PUT` (substitui) vs `PATCH` (parcial) na tarefa.

---

## 6. Projeto Final: Spring Security e Autenticação { #projeto-final }

**Unidade IX.**

**Pasta de entrega:** `backend/07-projeto-final-spring-security/` (veja a seção 0.3)

### Objetivo
Proteger a API do Mini Projeto 5 com autenticação **JWT** e autorização por perfil **e por dono do recurso**.

### Funcionalidades

1. **Cadastro e login:**
   - `POST /auth/registro` cria o usuário com a **senha em BCrypt** (campo `senhaHash`) e papel `USER`.
   - `POST /auth/login` devolve um JWT com expiração.
   - O campo de senha **nunca** aparece em nenhuma resposta.
2. **Autenticação stateless com JWT** (somente JWT; sessão não faz parte do projeto), com filtro `OncePerRequestFilter` e `SecurityFilterChain` como bean.
3. **Rotas públicas:** `/auth/**` e a documentação Swagger. **Todas as demais exigem token.**
4. **Perfis:** enum `Papel { USER, ADMIN }`. Apenas `ADMIN` pode remover usuários e listar **todos** os usuários.
5. **Autorização por dono do recurso:** um `USER` só pode **ver, editar e excluir as próprias tarefas**. Ao criar uma tarefa, o dono é o usuário **autenticado** (o `usuarioId` do corpo é ignorado). Listagens de tarefas devolvem somente as do usuário logado (o `ADMIN` vê todas).
6. **Erros de segurança no formato `ProblemDetail`:** sem token ou token inválido/expirado → `401`; sem permissão → `403`; credenciais erradas no login → `401` com mensagem genérica.
7. **Segredo do JWT em variável de ambiente.**

### Critérios de Aceite
**Obrigatórios:**
- Senhas guardadas **apenas** como hash BCrypt.
- Login devolve JWT; o token expira.
- **Sem token → `401`** (não `403`) em qualquer rota protegida.
- Usuário `USER` tentando mexer na tarefa de outro → `403`; `ADMIN` consegue.
- O cadastro público **não permite** escolher o papel `ADMIN`.
- O segredo do JWT **não está** no repositório (verificar histórico de commits e `application.properties`).
- **Fluxo de testes no Postman** documentado em `docs/fluxo-seguranca.md`: registro → login → uso do token → tentativa em recurso alheio (403) → sem token (401) → token adulterado (401).
- Pelo menos **3 testes automatizados** de segurança (sem token, papel insuficiente, dono vs. não dono).
- `AccessDeniedException` tratada no `@RestControllerAdvice` (nenhum `403` sendo devolvido como `500`).

**README (decisões de segurança):**
1. Por que o payload do JWT não pode ter dados sensíveis.
2. Por que o `csrf` pôde ser desativado nesta API.
3. Quando a tarefa existe mas é de outro usuário, a API devolve `403` ou `404`? Justifique.
4. O que acontece com um token válido se o usuário for removido? Quais os limites do modelo stateless?

---

## 7. Entrega e Revisão

### Checklist final de cada projeto

- [ ] Está na pasta correta, com o nome exato do mapa da seção 0.3, dentro de `backend/`.
- [ ] O `pom.xml` está na raiz da pasta do projeto, e o projeto abre sozinho na IDE.
- [ ] O `README.md` segue o modelo e tem as decisões de design pedidas pelo desafio.
- [ ] O projeto sobe com os comandos do README e os testes exigidos passam.
- [ ] Sem `target/`, `.idea/`, segredos nem arquivos grandes (vídeos entram como link).
- [ ] Coleções do Postman exportadas em `postman/` (a partir do projeto 06; no 03 também).
- [ ] A tabela de `backend/README.md` foi atualizada.
- [ ] Mexi **apenas** na pasta do projeto e em `backend/README.md`.

### Como a revisão funciona

1. O membro abre o Pull Request (seção 0.3) e solicita a revisão do **assessor de conhecimentos de Back-end**.
2. O assessor avalia pelos **critérios de avaliação (seção 0.4)** e pelos **critérios de aceite** do projeto, comentando no PR o que não atende aos obrigatórios.
3. O membro corrige na mesma branch até a aprovação.
4. Com o PR **aprovado**, o membro faz o merge na `main` e só então começa o próximo projeto.

---

[← Voltar para a Visão Geral do Back-end](index.md){ .md-button }
