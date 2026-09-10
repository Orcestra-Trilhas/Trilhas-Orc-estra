# Central de Projetos e Desafios — Mobile

---

Esta página contém o **Projeto Final obrigatório** da Trilha de Mobile. Os exercícios práticos de cada unidade estão no final das respectivas páginas e servem para fixar o conteúdo.

---

## 🏆 Projeto Final (Obrigatório)

### Objetivo

Este é o **único entregável obrigatório** da Trilha Mobile. O objetivo é consolidar **todos os conhecimentos** adquiridos ao longo das 6 unidades construindo um **app de trilha de aprendizado gamificado** — um "mini Duolingo" simplificado onde o usuário aprende sobre um tema de sua escolha através de lições e quizzes, ganhando pontos e acompanhando seu progresso.

!!! tip "Escolha um tema para o seu app!"
    Pode ser qualquer área de conhecimento: programação, idiomas, história, ciências, música, culinária, etc. O tema é livre — o importante é a experiência gamificada.

---

### Funcionalidades Obrigatórias

#### 🗺️ Mapa de Trilha (Tela Principal)
- Exibir uma trilha visual com as lições/módulos organizados em ordem (estilo mapa de fases)
- Indicar visualmente quais lições foram concluídas, qual está disponível e quais estão bloqueadas
- Mostrar o progresso geral do usuário (ex: "5 de 12 lições concluídas")

#### 📚 Tela de Lição (Quiz)
- Cada lição contém um conjunto de perguntas (mínimo 3 perguntas por lição)
- Tipos de pergunta: múltipla escolha e/ou verdadeiro ou falso
- Feedback visual imediato (acertou ✅ / errou ❌) a cada resposta
- Ao final da lição, exibir resumo: acertos, erros e XP ganho

#### 👤 Tela de Perfil
- Nome e avatar do usuário
- Total de XP acumulado
- Número de lições concluídas
- Sequência de dias (streak) — quantos dias seguidos o usuário estudou

#### 🧭 Navegação
- Tab Navigator com as telas: Trilha (mapa), Perfil
- Stack Navigator para navegar da trilha para a lição/quiz

#### 💾 Persistência
- Salvar o progresso do usuário com `AsyncStorage` (progresso, XP e streak sobrevivem ao fechar o app)

---

### Funcionalidades Bônus (opcionais, para ir além)

- 🏆 Sistema de níveis (ex: Iniciante → Intermediário → Avançado) baseado no XP
- 🔥 Animação de streak e celebração ao completar lições (confetti, vibração)
- 📊 Tela de ranking/leaderboard com dados mock (simular outros usuários)
- 🎨 Modo escuro (dark mode)
- ⏱️ Timer nas perguntas (tempo limite para responder)
- 🔔 Notificação diária lembrando o usuário de estudar (push notification com Expo)
- 📷 Upload de foto de perfil com `expo-image-picker`
- 🎵 Sons de feedback (acerto/erro) com `expo-av`

---

### Critérios de Aceite

- [ ] Mapa de trilha funcional com indicação de progresso
- [ ] Mínimo de 4 lições com pelo menos 3 perguntas cada
- [ ] Sistema de XP funcional (ganha pontos ao acertar)
- [ ] Feedback visual imediato nas respostas (acerto/erro)
- [ ] Tela de perfil com XP e lições concluídas
- [ ] Navegação funcional com React Navigation (Tab + Stack)
- [ ] Persistência do progresso com `AsyncStorage`
- [ ] Código organizado em componentes reutilizáveis
- [ ] README completo com instruções de setup e screenshots
- [ ] Repositório público no GitHub

---

## Recursos Complementares Gratuitos

Cursos em vídeo e playlists para aprofundar seus conhecimentos em desenvolvimento mobile:

| Recurso | Tipo | Link |
| :--- | :--- | :--- |
| React Native com Expo — Fundamentos (Sujeito Programador) | Playlist | [Assistir](https://www.youtube.com/playlist?list=PLJ_KhUnlXUPtbtLwaxxUxHqvcNQndmI4B) |
| Criando um App do ZERO com React Native (Rocketseat) | Curso | [Assistir](https://www.youtube.com/watch?v=k1vdmXDgMJI) |
| JavaScript para Iniciantes (Curso em Vídeo — Guanabara) | Playlist | [Assistir](https://www.youtube.com/playlist?list=PLHz_AreHm4dlsK3Nr9GVvXCbpQyHQl1o1) |
| TypeScript para Iniciantes (Matheus Battisti) | Curso | [Assistir](https://www.youtube.com/watch?v=lCemyQeSCV8) |
| CS50's Mobile App Development (Harvard) | Curso (inglês) | [Assistir](https://cs50.harvard.edu/mobile/) |
| React Native — Rocketseat Discover | Curso | [Assistir](https://www.rocketseat.com.br/discover) |
| Lynx JS — Primeiros Passos (DevSoutinho) | Vídeo | [Assistir](https://www.youtube.com/watch?v=eGRIBnRf-jE) |
| React Native Express | Curso interativo | [Acessar](https://www.reactnative.express/) |

---

[← Voltar para a Visão Geral da Trilha Mobile](index.md){ .md-button }
