# Unidade I – Fundamentos Mobile & Onboarding

## Módulo 1.1: Introdução ao Desenvolvimento Mobile

Entender o ecossistema mobile (Android & iOS), diferenças entre desenvolvimento nativo vs. multiplataforma (cross-platform), e os principais frameworks do mercado.

**Conceitos-Chave:**

- Diferenças entre Android (Kotlin/Java) e iOS (Swift)
- Abordagens: Nativo, Híbrido e Multiplataforma
- Frameworks multiplataforma: React Native, Flutter, Lynx
- Ciclo de vida de um app mobile
- Publicação nas lojas (Google Play Store e Apple App Store)

**Links:**

- 🎥 [Vídeo: Como funciona o Desenvolvimento Mobile? (Código Fonte TV)](https://www.youtube.com/watch?v=UaVfBHRMVkk)
- 🎥 [Vídeo: React Native vs Flutter — Qual escolher? (Rocketseat)](https://www.youtube.com/watch?v=dMkBSp8ROX0)
- 🎥 [Vídeo: O que é Desenvolvimento Mobile? Nativo vs Multiplataforma (Rafaella Ballerini)](https://www.youtube.com/watch?v=3dJJAB3aZbo)

---

## Módulo 1.2: IDEs & Ferramentas de Desenvolvimento

Conhecer, instalar e configurar as ferramentas que serão usadas ao longo de toda a trilha.

### VS Code

O **Visual Studio Code** é o editor recomendado para desenvolvimento com React Native e Lynx.

- 🌐 [Download VS Code](https://code.visualstudio.com/)

**Extensões Recomendadas:**

| Extensão | Descrição |
| :--- | :--- |
| [React Native Tools](https://marketplace.visualstudio.com/items?itemName=msjsdiag.vscode-react-native) | Depuração e IntelliSense para React Native |
| [ES7+ React/Redux/React-Native Snippets](https://marketplace.visualstudio.com/items?itemName=dsznajder.es7-react-js-snippets) | Snippets rápidos para componentes e hooks |
| [Prettier](https://marketplace.visualstudio.com/items?itemName=esbenp.prettier-vscode) | Formatação automática de código |
| [ESLint](https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint) | Análise estática e correção de erros |
| [Color Highlight](https://marketplace.visualstudio.com/items?itemName=naumovs.color-highlight) | Visualização de cores inline no editor |

### Android Studio (Emulador Android)

O **Android Studio** é necessário para rodar o emulador Android e compilar builds Android. Mesmo usando React Native, você precisará dele instalado.

- 🌐 [Download Android Studio](https://developer.android.com/studio)
- 🎥 [Vídeo: Como instalar e configurar o Android Studio (Sujeito Programador)](https://www.youtube.com/watch?v=gAd-8rFhpn0)

### Xcode (Somente macOS — Emulador iOS)

Se você está em um Mac, o **Xcode** é necessário para compilar e emular apps no iOS.

- 🌐 [Download Xcode (Mac App Store)](https://apps.apple.com/br/app/xcode/id497799835)

### Expo Go (Teste Rápido no Celular)

O **Expo** permite rodar aplicativos React Native diretamente no seu celular físico sem a necessidade de configurar emuladores nativos.

- 🌐 [Expo — Documentação Oficial](https://docs.expo.dev/)
- 📱 [Expo Go — Google Play](https://play.google.com/store/apps/details?id=host.exp.exponent)
- 📱 [Expo Go — App Store](https://apps.apple.com/app/expo-go/id982107779)

---

## Módulo 1.3: Configuração do Ambiente de Desenvolvimento

Instalar e configurar Node.js, o CLI do Expo e validar que o ambiente está funcional.

### Passo a Passo

1. **Instalar Node.js (LTS)** — Gerenciador de pacotes e runtime JavaScript.
    - 🌐 [Download Node.js](https://nodejs.org/pt)
2. **Instalar o Expo CLI** — Ferramenta de linha de comando para criar e gerenciar projetos React Native com Expo.

    ```bash
    npm install -g expo-cli
    ```

3. **Verificar instalação:**

    ```bash
    node --version
    npm --version
    expo --version
    ```

4. **Configurar variáveis de ambiente do Android SDK** (se usar emulador Android):
    - 🎥 [Vídeo: Configurando ambiente React Native (Rocketseat)](https://www.youtube.com/watch?v=nWAA7VqBiGc)

**Links Complementares:**

- 🎥 [Vídeo: Configurando ambiente React Native do ZERO em 2024 (Sujeito Programador)](https://www.youtube.com/watch?v=eSjk4HFiEBA)
- 🎥 [Vídeo: Como instalar o Expo e criar seu primeiro projeto (One Bit Code)](https://www.youtube.com/watch?v=aulGcEwMdag)

---

## 2. Próximo Passo

Com o ambiente de desenvolvimento instalado e configurado, avance para a **Unidade II** para dominar os fundamentos de JavaScript e TypeScript necessários para o desenvolvimento mobile.
