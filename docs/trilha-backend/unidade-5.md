# Unidade V – Protocolo & Métodos HTTP

---

## 1. Visão Geral

Para conectar o back-end ao mundo Web, precisamos entender a linguagem de comunicação da internet: o protocolo HTTP.

Nesta unidade, você aprenderá sobre verbos HTTP, cabeçalhos, parâmetros de URL, corpo da requisição e códigos de status. Ao final, você será capaz de interpretar e testar requisições HTTP como as que uma API REST recebe e responde no dia a dia.

---

## 2. Conceitos do Protocolo HTTP

### 2.1 Verbos (Métodos) HTTP

Definem a **intenção** da requisição sobre um recurso:

- **`GET`** — busca/lê um recurso, sem alterá-lo.
- **`POST`** — cria um novo recurso.
- **`PUT`** — atualiza um recurso existente por completo (substitui todos os campos).
- **`DELETE`** — remove um recurso.

### 2.2 Cabeçalhos (Headers)

Metadados enviados junto da requisição/resposta, que não fazem parte do "conteúdo" em si, mas orientam como ele deve ser tratado. Exemplos comuns: `Content-Type` (formato do corpo, ex.: `application/json`), `Authorization` (token de autenticação) e `Accept` (formato de resposta esperado pelo cliente).

### 2.3 Parâmetros de URL

Formas de enviar dados dentro da própria URL:

- **Path variables** — fazem parte do caminho, identificando um recurso específico. Ex.: `/tarefas/{id}`.
- **Query parameters** — vêm após `?`, geralmente usados para filtros, busca ou paginação. Ex.: `/tarefas?status=pendente&pagina=2`.

### 2.4 Corpo da Requisição (Body)

Usado em métodos como `POST` e `PUT` para enviar os dados do recurso em si — normalmente no formato **JSON**. É nele que vai, por exemplo, o nome e a descrição de uma nova Tarefa sendo criada.

### 2.5 Códigos de Status HTTP

Indicam o **resultado** de uma requisição, agrupados por faixa: `2xx` (sucesso), `4xx` (erro do cliente) e `5xx` (erro do servidor). Veja a tabela de referência na seção 3.

- 🎥 [Vídeo: Entendendo o Protocolo HTTP](https://www.youtube.com/watch?v=PcHbyGVoqZk)
- 🎥 [Vídeo: Desvendando Requisições HTTP (Métodos, Parâmetros, Body e Status Codes)](https://www.youtube.com/watch?v=bMmdksBHyXc&t=5s&pp=ygULaHR0cCBtZXRvZG8%3D)

---

## 3. Tabela de Referência: Códigos de Status HTTP

Confira os códigos de status HTTP mais utilizados em APIs REST desenvolvidas com Spring Boot:

| Código | Significado | Quando Usar no Spring Boot |
| :---: | :--- | :--- |
| `200 OK` | Sucesso | Retorno padrão para buscas (`GET`) e atualizações (`PUT`). |
| `201 Created` | Criado | Retorno para cadastro com sucesso (`POST`). |
| `400 Bad Request` | Requisição Inválida | Erro nos dados ou falha de validação enviada pelo cliente. |
| `404 Not Found` | Não Encontrado | Quando buscar um Usuário ou Tarefa que não existe no banco. |
| `500 Internal Error` | Erro no Servidor | Falha interna ou exceção não tratada na aplicação. |

---

## 4. Atividade Prática com Postman

!!! tip "Demonstração com Postman"
    Ao final desta unidade, utilize o **Postman** ou o **Insomnia** para criar uma coleção de testes simulando requisições HTTP e gravando um breve vídeo demonstrando os conceitos aprendidos.
