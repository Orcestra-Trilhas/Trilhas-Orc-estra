# Unidade VII – Spring Security & Autenticação

---

## 1. Visão Geral

Nesta unidade, você aprenderá sobre autenticação de usuários, criptografia de senhas (BCrypt), tokens e controle de acesso a rotas privadas. Ao final, sua API será capaz de identificar quem está fazendo a requisição e decidir o que essa pessoa pode ou não acessar.

---

## 2. Fundamentos do Spring Security

### 7.1 Autenticação vs. Autorização

São dois conceitos frequentemente confundidos, mas distintos:

- **Autenticação** responde "quem é você?" — o processo de verificar a identidade do usuário (ex.: validar login e senha).
- **Autorização** responde "o que você pode fazer?" — o processo de verificar se um usuário já autenticado tem permissão para acessar um determinado recurso ou rota.

O Spring Security trata os dois de forma separada, e é comum uma API exigir autenticação para tudo, mas liberar autorização diferente por perfil de usuário (ex.: `ADMIN` vs. `USER`).

### 7.2 Filter Chain (Cadeia de Filtros)

O Spring Security funciona interceptando **toda requisição HTTP** antes dela chegar ao Controller, passando-a por uma sequência de **filtros** (a `SecurityFilterChain`). Cada filtro tem uma responsabilidade específica — um verifica se existe um token válido, outro verifica se a rota exige autenticação, e assim por diante. Se a requisição passar por toda a cadeia sem ser bloqueada, ela finalmente chega ao Controller; caso contrário, o Spring Security já responde com erro (ex.: `401 Unauthorized`) antes mesmo do seu código de negócio ser executado.

### 7.3 UserDetails e UserDetailsService

Para autenticar alguém, o Spring Security precisa saber *onde* buscar os dados do usuário e *como* interpretá-los:

- **`UserDetails`**: interface que representa o usuário autenticado do ponto de vista do Spring Security (login, senha, permissões, se a conta está ativa/expirada, etc.).
- **`UserDetailsService`**: interface com um único método (`loadUserByUsername`), que você implementa para dizer ao Spring Security **como buscar** um usuário no seu banco de dados (via JPA, por exemplo) e transformá-lo em um `UserDetails`.

### 7.4 Criptografia de Senhas (PasswordEncoder / BCrypt)

Senhas **nunca** devem ser armazenadas em texto puro no banco. O Spring Security usa a interface `PasswordEncoder` para lidar com isso, sendo o **BCrypt** (`BCryptPasswordEncoder`) a implementação mais usada. O BCrypt é um algoritmo de **hash com salt automático** — ou seja, mesmo duas senhas idênticas geram hashes diferentes no banco, e o processo é intencionalmente lento (por design), o que dificulta ataques de força bruta.

### 7.5 Tokens e Autenticação Stateless (JWT)

Diferente de aplicações web tradicionais (que usam sessão no servidor), APIs REST costumam ser **stateless** — o servidor não guarda informação de quem está logado entre uma requisição e outra. Para resolver isso, usa-se **tokens**, sendo o **JWT (JSON Web Token)** o formato mais comum:

- No login, o servidor valida usuário/senha e gera um token assinado, contendo informações do usuário (ex.: ID, permissões) e uma validade.
- Nas requisições seguintes, o cliente envia esse token no cabeçalho (`Authorization: Bearer <token>`), e um filtro da Security Filter Chain valida a assinatura e extrai as informações do usuário — sem precisar consultar o banco a cada requisição só para saber "quem é" (a consulta ao banco geralmente só acontece no login).

### 7.6 Controle de Acesso a Rotas

Depois de autenticado, o Spring Security permite definir **quais rotas exigem quais permissões**, geralmente configurado na `SecurityFilterChain` (ex.: liberando `/auth/login` para todos, mas exigindo autenticação para `/tarefas/**`) ou de forma mais granular por método, usando anotações como `@PreAuthorize("hasRole('ADMIN')")` diretamente no Controller ou Service.

- 🎥 [Vídeo: Curso Spring Boot — Dominando o Spring Security](https://www.youtube.com/watch?v=KYa4xQaQ7SU&t=813s&pp=ygUJc291emEgZGV2)

---

## 3. Projeto Final da Trilha

Implemente autenticação e segurança na API do **Gerenciador de Tarefas**:

- 🌐 [Instruções do Projeto Final Spring Security ➔](desafio.md#4-projeto-final-spring-security-autenticacao)

---

## 4. Aprofundamento de Carreira

- 🌐 [Roadmap Completo de Carreira Back-end (Roadmap.sh)](https://roadmap.sh/backend)
