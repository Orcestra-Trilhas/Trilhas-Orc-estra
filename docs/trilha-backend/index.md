# Trilha de Back-end

---

## 1. Visão Geral e Objetivos

Este documento padroniza a capacitação interna da **Orc'estra Gamificação** para a área de **Back-end**.

O objetivo é levar o membro de "sei programar um pouco" até "consigo contribuir em um projeto Spring Boot real": construir APIs REST em camadas, persistir dados com JPA, tratar erros de forma padronizada, proteger rotas com JWT e escrever testes básicos.

!!! warning "Escopo"
    A trilha cobre o ciclo completo de uma API em Java. Ela **não** cobre front-end, DevOps avançado nem mensageria. Ao final, o membro estará apto a entrar em um projeto da empresa com acompanhamento, não a liderá-lo sozinho.

---

## 2. Pré-requisitos e Versões

**Pré-requisitos do aluno:** lógica de programação (variáveis, condicionais, laços, funções) em qualquer linguagem e noção básica de Git (clone, commit, push, branch). A Unidade II revisa a sintaxe de Java, mas não ensina lógica do zero.

**Versões de referência do material:**

| Ferramenta | Versão |
| :--- | :--- |
| Java (JDK) | 21 |
| Spring Boot | 3.x (3.4 ou superior) |
| Spring Security | 6.x |
| Build | Maven (via Maven Wrapper) |

!!! note "Se você usar Spring Boot 4.x"
    Algumas coisas mudam entre versões maiores (nomes de starters, biblioteca de JSON, anotações de teste). Se o projeto for criado com uma versão diferente da de referência, consulte o guia de migração oficial do Spring Boot antes de seguir os trechos de código.

---

## 3. Ordem de Estudo

A ordem importa: cada unidade usa o que a anterior ensinou.

```text
I Ambiente -> II POO -> III Java Moderno -> IV Banco de Dados -> V HTTP/REST
      -> VI Spring Boot -> VII Spring Data JPA -> VIII Spring Web -> IX Spring Security
```

Cada unidade termina com uma **checklist de conclusão** e um projeto prático descrito em [desafio.md](desafio.md). Só avance quando conseguir marcar todos os itens da checklist.

---

## Unidades de Aprendizado

<div class="trilhas-grid">
  <a href="unidade-1/" class="trilha-card">
    <h3>Unidade I: Preparando o Ambiente</h3>
    <p>JDK, IDE, Maven, Spring Initializr e o primeiro projeto rodando.</p>
    <span class="trilha-card-link">Acessar Unidade ➔</span>
  </a>

  <a href="unidade-2/" class="trilha-card">
    <h3>Unidade II: Java (POO)</h3>
    <p>Sintaxe, classes, enums, os 4 pilares da POO, exceções e o contrato equals/hashCode.</p>
    <span class="trilha-card-link">Acessar Unidade ➔</span>
  </a>

  <a href="unidade-3/" class="trilha-card">
    <h3>Unidade III: Java Moderno e Estruturas de Dados</h3>
    <p>Collections, lambdas, Stream API, Optional, Generics, Packages, Records e java.time.</p>
    <span class="trilha-card-link">Acessar Unidade ➔</span>
  </a>

  <a href="unidade-4/" class="trilha-card">
    <h3>Unidade IV: Banco de Dados</h3>
    <p>Modelagem, SQL básico, normalização, transações (ACID) e H2 Database.</p>
    <span class="trilha-card-link">Acessar Unidade ➔</span>
  </a>

  <a href="unidade-5/" class="trilha-card">
    <h3>Unidade V: HTTP e REST</h3>
    <p>Protocolo HTTP, verbos, status codes, princípios REST, CORS e testes com Postman.</p>
    <span class="trilha-card-link">Acessar Unidade ➔</span>
  </a>

  <a href="unidade-6/" class="trilha-card">
    <h3>Unidade VI: Spring Boot</h3>
    <p>IoC, DI, Beans, auto-configuração, arquitetura em camadas, configuração e primeiros testes.</p>
    <span class="trilha-card-link">Acessar Unidade ➔</span>
  </a>

  <a href="unidade-7/" class="trilha-card">
    <h3>Unidade VII: Spring Data JPA</h3>
    <p>ORM, entidades, relacionamentos, herança, consultas, N+1 e transações.</p>
    <span class="trilha-card-link">Acessar Unidade ➔</span>
  </a>

  <a href="unidade-8/" class="trilha-card">
    <h3>Unidade VIII: Spring Web</h3>
    <p>Controllers REST, DTOs, validação, erros padronizados (ProblemDetail), Swagger e testes de API.</p>
    <span class="trilha-card-link">Acessar Unidade ➔</span>
  </a>

  <a href="unidade-9/" class="trilha-card">
    <h3>Unidade IX: Spring Security</h3>
    <p>Autenticação, BCrypt, JWT, autorização por perfil e por dono do recurso.</p>
    <span class="trilha-card-link">Acessar Unidade ➔</span>
  </a>
</div>

---

## Central de Projetos Práticos

Os projetos acompanham a evolução de **um único sistema** (o Gerenciador de Tarefas): do terminal até uma API segura. Veja o domínio, as convenções de nomes, o formato de entrega e os critérios de avaliação na Central de Desafios:

[Ir para os Desafios Práticos ➔](desafio.md){ .md-button .md-button--primary }
