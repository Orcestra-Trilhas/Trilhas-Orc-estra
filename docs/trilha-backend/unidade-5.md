# Unidade V: HTTP e REST

---

## 1. Visão Geral

Para conectar o back-end ao mundo Web, precisamos entender a linguagem de comunicação da internet: o protocolo **HTTP**, e o estilo de projetar APIs em cima dele: **REST**.

Ao final, você será capaz de interpretar e testar requisições HTTP como as que uma API recebe e responde no dia a dia, e de desenhar rotas e respostas seguindo as convenções REST.

---

## 2. Conceitos do Protocolo HTTP

### 5.1 Anatomia de uma Requisição e de uma Resposta

```http
POST /tarefas HTTP/1.1
Host: localhost:8080
Content-Type: application/json
Authorization: Bearer eyJhbGciOi...

{ "titulo": "Estudar JPA", "usuarioId": 1 }
```

```http
HTTP/1.1 201 Created
Content-Type: application/json
Location: /tarefas/7

{ "id": 7, "titulo": "Estudar JPA", "concluida": false }
```

Uma requisição tem: **método**, **URL**, **headers** e (às vezes) **body**. Uma resposta tem: **status code**, **headers** e (às vezes) **body**.

### 5.2 Verbos (Métodos) HTTP

Definem a **intenção** da requisição sobre um recurso:

- **`GET`**: busca/lê um recurso, sem alterá-lo.
- **`POST`**: cria um novo recurso.
- **`PUT`**: **substitui** um recurso existente por completo (todos os campos).
- **`PATCH`**: atualiza **parcialmente** um recurso (só os campos enviados).
- **`DELETE`**: remove um recurso.

**PUT vs PATCH:** para mudar só o título de uma tarefa, `PATCH` envia `{ "titulo": "Novo" }`. Com `PUT` você precisa enviar o recurso inteiro; os campos omitidos tendem a ser zerados.

**Métodos seguros e idempotentes:**

| Método | Seguro (não altera dados) | Idempotente (repetir dá o mesmo resultado) |
| :---: | :---: | :---: |
| `GET` | sim | sim |
| `PUT` | não | sim |
| `DELETE` | não | sim |
| `PATCH` | não | não necessariamente |
| `POST` | não | **não** (cada chamada pode criar um novo recurso) |

Idempotência importa na prática: se a conexão cair, o cliente pode repetir um `PUT` ou `DELETE` sem medo; repetir um `POST` pode duplicar o cadastro.

### 5.3 Cabeçalhos (Headers)

Metadados da requisição/resposta. Os mais comuns: `Content-Type` (formato do corpo, ex.: `application/json`), `Accept` (formato esperado na resposta), `Authorization` (credencial), `Location` (URL do recurso criado).

### 5.4 Parâmetros de URL

- **Path variables:** fazem parte do caminho e identificam um recurso. Ex.: `/tarefas/{id}`.
- **Query parameters:** vêm após `?`, para filtros, busca, ordenação e paginação. Ex.: `/tarefas?concluida=false&page=0&size=10`.

### 5.5 Corpo da Requisição (Body)

Usado em `POST`, `PUT` e `PATCH` para enviar os dados do recurso, normalmente em **JSON**.

### 5.6 Códigos de Status HTTP

Indicam o **resultado** da requisição, por faixa: `2xx` sucesso, `3xx` redirecionamento, `4xx` erro do **cliente**, `5xx` erro do **servidor**. Veja a tabela na seção 4.

- 🎥 [Vídeo: Entendendo o Protocolo HTTP](https://www.youtube.com/watch?v=PcHbyGVoqZk)
- 🎥 [Vídeo: Desvendando Requisições HTTP (Métodos, Parâmetros, Body e Status Codes)](https://www.youtube.com/watch?v=bMmdksBHyXc&t=5s&pp=ygULaHR0cCBtZXRvZG8%3D)

---

## 3. REST

**REST** (*Representational State Transfer*) é um conjunto de princípios para projetar APIs sobre HTTP. Uma API que segue esses princípios é chamada "RESTful". Os pontos que importam no dia a dia:

1. **Tudo é um recurso**, identificado por uma URL. O recurso é um **substantivo** no **plural**: `/usuarios`, `/tarefas`. O **verbo HTTP** diz a ação.
   - Certo: `DELETE /tarefas/7`
   - Errado: `POST /deletarTarefa`, `GET /buscarTodasAsTarefas`
2. **Relações viram sub-rotas:** `GET /usuarios/3/tarefas` (tarefas do usuário 3). Evite aninhar mais de dois níveis.
3. **Stateless (sem estado):** cada requisição leva tudo o que o servidor precisa para atendê-la (inclusive a credencial). O servidor não "lembra" de requisições anteriores. É por isso que APIs usam tokens (Unidade IX).
4. **Representações:** o cliente nunca recebe o "objeto do banco", e sim uma **representação** dele, geralmente JSON.
5. **Status codes com significado:** criou, devolva `201`; não achou, `404`. Não responda `200` com `{ "erro": "..." }` no corpo.

---

## 4. Tabela de Referência: Códigos de Status HTTP

| Código | Significado | Quando usar em uma API REST |
| :---: | :--- | :--- |
| `200 OK` | Sucesso | `GET` e `PUT`/`PATCH` bem-sucedidos que devolvem o recurso. |
| `201 Created` | Criado | `POST` que criou um recurso (envie o header `Location`). |
| `204 No Content` | Sucesso sem corpo | `DELETE` bem-sucedido (padrão) ou atualização sem retorno. |
| `400 Bad Request` | Requisição inválida | JSON malformado ou falha de validação dos dados enviados. |
| `401 Unauthorized` | Não autenticado | Falta credencial ou ela é inválida/expirada. ("quem é você?") |
| `403 Forbidden` | Proibido | Autenticado, mas **sem permissão** para o recurso. ("você não pode") |
| `404 Not Found` | Não encontrado | O recurso (usuário, tarefa) não existe. |
| `409 Conflict` | Conflito | Violação de unicidade (ex.: e-mail já cadastrado). |
| `500 Internal Server Error` | Erro no servidor | Falha inesperada (bug, banco fora do ar). Nunca exponha o stack trace ao cliente. |

---

## 5. CORS

Quando um front-end em `http://localhost:3000` chama sua API em `http://localhost:8080`, o **navegador** bloqueia a resposta por padrão (política de mesma origem). **CORS** (*Cross-Origin Resource Sharing*) é o mecanismo pelo qual o **servidor** declara quais origens podem chamá-lo, via headers como `Access-Control-Allow-Origin`.

Para métodos como `PUT`/`DELETE` ou requisições com `Authorization`, o navegador antes envia uma requisição `OPTIONS` (*preflight*) perguntando se pode. Se o servidor não responder o que o navegador espera, o front-end vê o famoso "blocked by CORS policy".

Pontos importantes:
- CORS é uma proteção do **navegador**. Postman e `curl` **não** são afetados, então "funciona no Postman, mas não no front" quase sempre é CORS.
- A correção é no **back-end** (você verá como configurar na Unidade VIII).

---

## 6. Atividade Prática com Postman

!!! tip "Demonstração com Postman"
    Como você ainda não tem uma API própria, pratique contra uma API pública de testes, como o [JSONPlaceholder](https://jsonplaceholder.typicode.com). Ela aceita `GET`, `POST`, `PUT`, `PATCH` e `DELETE` em `/posts`, `/users` e outros recursos (as escritas são simuladas e não persistem, o que é suficiente para treinar).

    Use o **Postman** ou o **Insomnia** para criar uma coleção que exercite cada verbo, mostre os status codes recebidos, use um query parameter e um path variable, e envie um body JSON. Grave um vídeo curto explicando o que cada requisição faz.

    Depois, a mesma coleção será reaproveitada contra a **sua** API nas Unidades VIII e IX.

---

## 7. Checklist de Conclusão

- [ ] Explico a diferença entre `PUT` e `PATCH` e entre método seguro e idempotente.
- [ ] Sei por que `POST` não é idempotente e qual o risco disso.
- [ ] Diferencio `401` de `403` e sei quando usar `204`, `404` e `409`.
- [ ] Corrijo nomes de rota ruins (`/criarTarefa`) para o padrão REST.
- [ ] Explico por que "funciona no Postman, mas não no front" aponta para CORS.
- [ ] Entreguei a coleção do Postman e o vídeo da atividade.

---

## 8. Projeto Prático

- 🌐 [Atividade 3: Coleção Postman e HTTP ➔](desafio.md#atividade-3)
