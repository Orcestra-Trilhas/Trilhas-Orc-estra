# Spring Web – Controllers e APIs REST

---

## 1. Visão Geral

Enquanto o Spring Data JPA cuida da persistência, o **Spring Web** (parte do módulo `spring-boot-starter-web`) é responsável por expor sua aplicação como uma API HTTP — recebendo requisições, extraindo dados delas e devolvendo respostas.

Aqui você vai entender como uma requisição HTTP chega até o seu código, como mapear rotas, como extrair dados da URL/corpo da requisição, como formatar respostas corretamente e como tratar erros de forma centralizada.

Ao final, você será capaz de construir Controllers REST completos, seguindo as boas práticas de status code, validação e tratamento de exceções.

---

## 2. Como uma Requisição Chega ao Controller

Antes das anotações, vale entender o caminho que uma requisição percorre dentro do Spring Web:

- **`DispatcherServlet`**: é o "front controller" do Spring MVC — todo request HTTP passa primeiro por ele. É o `DispatcherServlet` quem consulta o **Handler Mapping** para descobrir qual Controller/método deve tratar aquela URL e verbo HTTP, e depois delega a chamada para ele.
- **Handler Mapping**: é o mecanismo que casa uma URL + verbo HTTP com o método correto, com base nas anotações `@RequestMapping`/`@GetMapping`/etc. declaradas nos Controllers.
- Depois que o método do Controller retorna um valor, o `DispatcherServlet` repassa esse retorno para um **`HttpMessageConverter`** (geralmente o Jackson, para JSON), que serializa o objeto Java de volta em JSON antes de enviar a resposta.

Esse fluxo (`Requisição → DispatcherServlet → Handler Mapping → Controller → HttpMessageConverter → Resposta`) acontece automaticamente com o Spring Boot — você só precisa declarar os Controllers.

---

## 3. Criando um Controller REST

- **`@RestController`**: combina `@Controller` (marca a classe como um Bean gerenciado pelo Spring, apto a receber requisições) com `@ResponseBody` (indica que o retorno de cada método deve ser escrito diretamente no corpo da resposta, já serializado como JSON, em vez de resolver para uma view HTML).
- **`@RequestMapping("/tarefas")`**: define um prefixo de rota para todos os métodos da classe (opcional, mas comum para organizar por recurso).

```java
@RestController
@RequestMapping("/tarefas")
public class TarefaController {
    // métodos aqui
}
```

---

## 4. Mapeando Rotas por Verbo HTTP

Cada verbo HTTP (visto na Unidade V) tem sua anotação correspondente, todas variações especializadas de `@RequestMapping`:

```java
@GetMapping                  // GET  /tarefas
@GetMapping("/{id}")         // GET  /tarefas/{id}
@PostMapping                 // POST /tarefas
@PutMapping("/{id}")         // PUT  /tarefas/{id}
@DeleteMapping("/{id}")      // DELETE /tarefas/{id}
```

Cada uma delas é usada no método do Controller que deve tratar aquele verbo + rota específicos.

---

## 5. Extraindo Dados da Requisição

O Spring Web oferece anotações para extrair, automaticamente, cada parte de uma requisição HTTP (revistas na Unidade V) direto como parâmetro do método:

- **`@PathVariable`**: extrai um valor da própria URL (path variable). Ex.: `/tarefas/{id}` → `@PathVariable Long id`.
- **`@RequestParam`**: extrai um query parameter. Ex.: `/tarefas?status=pendente` → `@RequestParam String status`. Pode ser opcional com `@RequestParam(required = false)` ou ter valor padrão com `defaultValue`.
- **`@RequestBody`**: converte o corpo JSON da requisição diretamente em um objeto Java (usando o `HttpMessageConverter` visto na seção 2). Usado em `POST`/`PUT`.
- **`@RequestHeader`**: extrai um cabeçalho específico da requisição. Ex.: `@RequestHeader("Authorization") String token`.

```java
@GetMapping
public List<Tarefa> listar(@RequestParam(required = false) Boolean concluida) { ... }

@PostMapping
public Tarefa criar(@RequestBody Tarefa novaTarefa) { ... }

@GetMapping("/{id}")
public Tarefa buscarPorId(@PathVariable Long id) { ... }
```

---

## 6. Formatando a Resposta: `ResponseEntity`

Retornar o objeto diretamente (como nos exemplos acima) funciona, mas sempre devolve `200 OK`. Para controlar explicitamente o **status code** (visto na Unidade V) e os headers da resposta, usa-se `ResponseEntity<T>`:

```java
@PostMapping
public ResponseEntity<Tarefa> criar(@RequestBody Tarefa novaTarefa) {
    Tarefa salva = tarefaService.salvar(novaTarefa);
    return ResponseEntity.status(HttpStatus.CREATED).body(salva);
}

@GetMapping("/{id}")
public ResponseEntity<Tarefa> buscarPorId(@PathVariable Long id) {
    return tarefaService.buscarPorId(id)
        .map(ResponseEntity::ok)
        .orElse(ResponseEntity.notFound().build());
}
```

Isso permite, por exemplo, devolver `201 Created` em um cadastro bem-sucedido, ou `404 Not Found` quando um recurso não existe — em vez de sempre `200`.

---

## 7. Validação de Dados (`@Valid`)

Para garantir que os dados recebidos no `@RequestBody` são válidos antes de chegarem à lógica de negócio, combina-se o **Bean Validation** (anotações como `@NotBlank`, `@NotNull`, `@Size`, `@Email` na própria classe/DTO) com `@Valid` no parâmetro do Controller:

```java
public class TarefaRequest {
    @NotBlank(message = "A descrição é obrigatória")
    private String descricao;
}

@PostMapping
public ResponseEntity<Tarefa> criar(@Valid @RequestBody TarefaRequest request) { ... }
```

Se a validação falhar, o Spring lança automaticamente uma `MethodArgumentNotValidException` — antes mesmo do seu código de negócio ser executado — que pode ser capturada e formatada como resposta de erro (próxima seção).

---

## 8. Tratamento Centralizado de Erros

Em vez de tratar exceções manualmente dentro de cada método do Controller (com `try/catch` repetido), o Spring Web permite centralizar esse tratamento:

- **`@ExceptionHandler`**: método que captura um tipo específico de exceção e define a resposta a ser devolvida.
- **`@ControllerAdvice`**: aplica os `@ExceptionHandler` de uma classe a **todos** os Controllers da aplicação, centralizando o tratamento de erros em um único lugar.

```java
@ControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(TarefaNaoEncontradaException.class)
    public ResponseEntity<String> tratarNaoEncontrada(TarefaNaoEncontradaException ex) {
        return ResponseEntity.status(HttpStatus.NOT_FOUND).body(ex.getMessage());
    }

    @ExceptionHandler(MethodArgumentNotValidException.class)
    public ResponseEntity<Map<String, String>> tratarValidacao(MethodArgumentNotValidException ex) {
        // monta um corpo de erro com os campos inválidos
    }
}
```

Isso evita repetir lógica de tratamento de erro em cada Controller e padroniza o formato das respostas de erro em toda a API.

---

## 9. Conteúdo em Vídeo

- 🎥 [Vídeo: Spring Web / Spring MVC na prática](https://www.youtube.com/watch?v=YuzWGKSzEcE&t=2707s&pp=ygUJc291emEgZGV2)

---

## 10. Projeto Prático

Implemente os endpoints REST completos (rotas, validação e tratamento de erros) do **Gerenciador de Tarefas**:

- 🌐 [Instruções do Mini Projeto Spring Web ➔](desafio.md#mini-projeto-spring-web)
