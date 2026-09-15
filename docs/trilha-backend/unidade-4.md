# Unidade IV – Banco de Dados & Modelagem

---

## 1. Visão Geral

Chegamos a uma etapa crucial do desenvolvimento back-end: a persistência e organização das informações que a aplicação utilizará.

Nesta unidade, você aprenderá a modelar bancos de dados nos níveis conceitual, lógico e físico, entendendo relacionamentos e cardinalidades. Ao final, você será capaz de projetar a estrutura de dados de um sistema antes mesmo de escrever a primeira linha de SQL.

---

## 2. Orientação de Ambiente: H2 Database

!!! note "Banco de Dados em Memória (H2)"
    Para esta unidade, **não instale** PostgreSQL ou MySQL localmente de cara. Utilizaremos o **H2 Database** (banco em memória). Ele roda junto com a aplicação Java, não exige instalação na máquina e elimina problemas de compatibilidade do tipo "na minha máquina não funciona".

---

## 3. Modelagem de Dados

### 3.1 Modelo Conceitual

É a etapa mais abstrata da modelagem, focada em **entender o problema do negócio**, sem se preocupar com tecnologia. Aqui você identifica as principais **entidades** (ex.: `Usuário`, `Tarefa`) e como elas se relacionam entre si, geralmente representadas em um **DER (Diagrama Entidade-Relacionamento)**.

### 3.2 Modelo Lógico

Refina o modelo conceitual definindo **atributos, chaves primárias (PK) e chaves estrangeiras (FK)**, além das **cardinalidades** entre entidades:

- **1:1** — um registro de uma entidade se relaciona com no máximo um registro de outra.
- **1:N** — um registro de uma entidade se relaciona com vários registros de outra (ex.: um `Usuário` tem várias `Tarefas`).
- **N:N** — vários registros de uma entidade se relacionam com vários de outra (geralmente resolvido com uma tabela associativa).

Ainda é independente do banco de dados específico que será usado (PostgreSQL, MySQL, etc.).

### 3.3 Modelo Físico

É a etapa final, onde o modelo lógico é traduzido para a **sintaxe real do SGBD escolhido** — tipos de dados específicos (`VARCHAR`, `SERIAL`, etc.), constraints, índices e o próprio script `CREATE TABLE`. É o modelo pronto para ser executado no banco de dados.

- 🎥 [Vídeo: Modelagem de Dados Nível Conceitual, Lógico e Físico (Cardinalidades)](https://www.youtube.com/watch?v=aWrka4it4Qs)
- 🎥 [Vídeo: Modelagem de Banco de Dados (Canal do Javão)](https://www.youtube.com/watch?v=SYqfqrx_b00)

---

## 4. Projeto Prático de Modelagem

Elabore a Modelagem Conceitual e Lógica do sistema **Gerenciador de Tarefas**:

- 🌐 [Instruções do Mini Projeto Banco de Dados ➔](desafio.md#2-mini-projeto-2-modelagem-do-banco-de-dados)
