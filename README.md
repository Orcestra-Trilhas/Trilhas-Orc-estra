# Trilhas de Capacitação — Orc’estra

## Link do Pages
https://Orcestra-trilhas.github.io/Trilhas-Orc-estra/

Repositório central das trilhas de conhecimento e capacitação técnica da Orc’estra Gamificação.

## Como Editar as Trilhas

O site inteiro é gerado a partir de arquivos Markdown (`.md`). Para adicionar ou alterar conteúdos:

1. Navegue até a pasta `docs/` neste repositório.
2. Edite os arquivos `.md` usando seu editor favorito.
3. Salve o arquivo.


## Como Executar Localmente

Para garantir que o projeto rode perfeitamente para todo mundo da Orc'estra, nós usamos o **Docker**. 

Isso significa que você **não precisa** instalar o Python, se preocupar com versões ou lidar com ambientes virtuais. O Docker prepara todo o ambiente nos bastidores para você!

### Pré-requisito
Você só precisa ter o **Docker** instalado e rodando no seu computador. 

### Passo a Passo

1. Abra o terminal na pasta raiz deste projeto.
2. Digite o comando mágico abaixo e aperte Enter:
```bash
docker compose up
```
3. Assim que o terminal carregar, abra o seu navegador e acesse: 👉 **[http://localhost:8000](http://localhost:8000)**

**Como parar o servidor:** Quando terminar de trabalhar, basta ir no terminal onde o site está rodando e apertar `Ctrl + C`.


## Padrões de Branches e Commits

Para mantermos a organização do projeto e sabermos exatamente o que cada pessoa está alterando, adotamos algumas regras simples para nomear as branches e os commits.

### Padrão de Branches
Sempre que for criar ou alterar o conteúdo de uma trilha, crie uma nova branch seguindo o formato `docs/nome-da-trilha` (tudo minúsculo e separado por traços).

* ✅ **Certo:** `docs/gamificacao`
* ✅ **Certo:** `docs/backend`
* ❌ **Errado:** `TrilhaGamificacao` ou `atualizando-textos`

### Padrão de Commits
Nossos commits seguem uma estrutura direta para que o histórico fique legível: `tipo: descrição clara do que foi feito`.

Os tipos mais comuns que você vai usar são:
* `docs:` Para criação, alteração ou exclusão de textos nas trilhas (Este será o mais usado!).
* `fix:` Para correção de pequenos erros, como links quebrados ou erros de digitação.
* `config:` Para alterações técnicas, como mudanças no `mkdocs.yml`, `requirements.txt` ou `docker-compose.yml`.

**Exemplos práticos:**
* ✅ `docs: adiciona o modulo 1 da trilha de hexad`
* ✅ `fix: corrige o link quebrado na trilha de onboarding`
* ✅ `config: adiciona o plugin de busca no mkdocs`
* ❌ `atualizei a trilha` (muito vago)

> **Dica de ouro:** Pense no commit como se você estivesse completando a frase: *"Se aplicado, este commit vai..."*

### Regras para Pull Requests (PR)

Nosso fluxo de trabalho usa a branch `develop` como ambiente de homologação e testes. 

Por isso, **todo Pull Request deve ser apontado para a branch `develop`**, e nunca direto para a `main`. 

1. Finalizou seu texto na sua branch `docs/sua-trilha`?
2. Faça o Push para o GitHub.
3. Abra o Pull Request apontando para a `develop` e peça revisão para outro membro da equipe.
