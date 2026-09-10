# Unidade VI – Lynx

**Tempo Estimado**: 2 semanas

Nesta unidade, você será apresentado ao **Lynx** — um framework de interface que utiliza tecnologias web (HTML, CSS e JavaScript/TypeScript) para construir interfaces nativas de alto desempenho em múltiplas plataformas. Diferente do React Native, o Lynx renderiza diretamente na thread nativa, resultando em animações mais fluidas e menor consumo de memória.

---

## Módulo 6.1: Introdução ao Lynx

Entender o que é o Lynx, como ele se diferencia de outros frameworks mobile, sua arquitetura e casos de uso.

**Conceitos-Chave:**

- O que é o Lynx e por que foi criado
- Arquitetura: Engine dual-thread (UI thread + JS thread)
- Diferenças entre Lynx, React Native e Flutter
- Quando usar Lynx vs. React Native
- Ecossistema: ReactLynx, templates e ferramentas

**Links:**

- 🌐 [Lynx — Site Oficial](https://lynxjs.org/)
- 🎥 [Vídeo: Lynx JS — O Novo Framework Mobile da ByteDance (Filipe Deschamps)](https://www.youtube.com/watch?v=CLkCKR-NRyY)
- 🎥 [Vídeo: Lynx — O framework que promete substituir React Native? (Lucas Montano)](https://www.youtube.com/watch?v=jPQaSQhR4EE)
- 🎥 [Vídeo: LynxJS — Primeiras impressões e review (Rocketseat)](https://www.youtube.com/watch?v=Tx2VUGqFDds)

---

## Módulo 6.2: Configuração do Ambiente Lynx

Instalar e configurar o ambiente de desenvolvimento para projetos Lynx.

**Pré-requisitos:**

- Node.js (>= 18) já instalado (da Unidade I)
- VS Code configurado

**Passo a passo:**

1. **Criar um novo projeto Lynx:**
    ```bash
    npm create rspeedy@latest -- --template react-ts
    ```

2. **Instalar dependências:**
    ```bash
    cd meu-projeto-lynx
    npm install
    ```

3. **Iniciar o servidor de desenvolvimento:**
    ```bash
    npm run dev
    ```

4. **Visualizar no dispositivo:**
    - Utilize o **LynxExplorer** (app de preview) para testar no celular
    - 📖 [Lynx Docs: Quick Start](https://lynxjs.org/guide/start/quick-start.html)

**Links:**

- 🎥 [Vídeo: Primeiros Passos com Lynx (DevSoutinho — Mario Souto)](https://www.youtube.com/watch?v=eGRIBnRf-jE)
- 🎥 [Vídeo: Configurando projeto Lynx do ZERO (Felipe Motta)](https://www.youtube.com/watch?v=pRxVP_Y_bvk)

---

## Módulo 6.3: Fundamentos do ReactLynx

Aprender a construir interfaces com **ReactLynx** — a camada React do Lynx que permite usar JSX e componentes React para construir UIs nativas.

**Conceitos-Chave:**

- Componentes básicos: `<view>`, `<text>`, `<image>`, `<scroll-view>`
- Estilização com CSS inline e StyleSheets
- Diferenças de sintaxe em relação ao React Native
- Eventos de toque e interação (`bindtap`)
- Renderização condicional e listas

**Links:**

- 🎥 [Vídeo: Construindo interfaces com ReactLynx (DevSoutinho)](https://www.youtube.com/watch?v=EF6Y3BPkXn0)
- 🌐 [Lynx Docs: Built-in Components](https://lynxjs.org/api/elements/built-in/view.html)

---

## Módulo 6.4: Navegação & Estado no Lynx

Aprender a gerenciar navegação entre telas e estado da aplicação no ecossistema Lynx.

**Conceitos-Chave:**

- Navegação entre páginas com `Navigator`
- Passagem de dados entre páginas
- Gerenciamento de estado com hooks (`useState`, `useEffect`)
- Comunicação entre a camada JS e a camada nativa

**Links:**

- 🎥 [Vídeo: Navegação e estado no Lynx (DevSoutinho)](https://www.youtube.com/watch?v=H7nwCSKfRYs)
- 🌐 [Lynx Docs: Navigating Between Pages](https://lynxjs.org/guide/navigation.html)

---

## Desafios da Unidade VI

!!! example "[Exercício 1] Cartão de Perfil com Lynx"
    Reconstrua o app **Meu Perfil** (desenvolvido na Unidade III) utilizando Lynx. Ao final, compare:

    - Sintaxe e estrutura de componentes
    - Performance percebida (fluidez de scroll e animações)
    - Tamanho do bundle
    - Experiência de desenvolvimento (DX)

    Documente suas conclusões em um breve relatório no README do repositório.

---

## Próximo Passo

Com o conhecimento de React Native e Lynx, você está pronto para o **Desafio Final** — o projeto obrigatório que consolida todo o aprendizado da Trilha Mobile.

[Ir para os Desafios Práticos ➔](desafio.md){ .md-button .md-button--primary }
