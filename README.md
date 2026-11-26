# Trilhas de Capacitação — Orc’estra

[![CI/CD Trilhas — Validação e Deploy](https://github.com/Orcestra-Trilhas/Trilhas-Orc-estra/actions/workflows/deploy.yml/badge.svg)](https://github.com/Orcestra-Trilhas/Trilhas-Orc-estra/actions/workflows/deploy.yml)

Repositório central das trilhas de conhecimento e capacitação técnica da **Orc’estra Gamificação**.

- **Site publicado:** https://Orcestra-trilhas.github.io/Trilhas-Orc-estra/
- **Repositório:** https://github.com/Orcestra-Trilhas/Trilhas-Orc-estra
- **Guia de contribuição:** [contributing.md](contributing.md)

## Sobre o projeto

O portal é gerado com **MkDocs** e o tema **Material**, a partir de arquivos Markdown mantidos neste repositório. Cada trilha é organizada em unidades de conteúdo e desafios práticos para consolidar o aprendizado da equipe. O site é publicado automaticamente no GitHub Pages após a validação do build.

## Trilhas disponíveis

| Trilha | Conteúdo |
| --- | --- |
| [Front-end](https://Orcestra-trilhas.github.io/Trilhas-Orc-estra/trilha-frontend/) | HTML, CSS, JavaScript e React |
| [Back-end](https://Orcestra-trilhas.github.io/Trilhas-Orc-estra/trilha-backend/) | Java POO, estruturas de dados, banco de dados, Spring Boot e Spring Security |
| [DevOps](https://Orcestra-trilhas.github.io/Trilhas-Orc-estra/trilha-devops/) | Sistemas operacionais, redes, contêineres, cloud, DevSecOps e troubleshooting |
| [Design (Figma)](https://Orcestra-trilhas.github.io/Trilhas-Orc-estra/trilha-figma/) | UX/UI, Auto Layout, componentização e prototipagem |
| [Gamificação](https://Orcestra-trilhas.github.io/Trilhas-Orc-estra/trilha-gamificacao/) | Psicologia comportamental, perfis de jogadores, jornada e PGG |
| [Mobile](https://Orcestra-trilhas.github.io/Trilhas-Orc-estra/trilha-mobile/) | React Native, navegação, estado, APIs nativas e Lynx |
| [Eletrônica & Embarcados](https://Orcestra-trilhas.github.io/Trilhas-Orc-estra/trilha-embarcados/) | ESP32, Raspberry Pi Pico 2W, MicroPython, IoT, Wokwi e PCB no KiCad |
| [LinkedIn](https://Orcestra-trilhas.github.io/Trilhas-Orc-estra/trilha-linkedin/) | Fundamentos e identidade, otimização do perfil, experiências, networking e produção de conteúdo |

## Estrutura do repositório

```text
.
├── docs/                  # Conteúdo do site em Markdown
│   ├── index.md           # Página inicial com os cards das trilhas
│   ├── assets/            # Logotipo e folha de estilos (extra.css)
│   └── trilha-*/          # Uma pasta por trilha: unidades, desafios e materiais
├── mkdocs.yml             # Configuração do site, tema e navegação
├── requirements.txt       # Dependências: mkdocs e mkdocs-material
├── docker-compose.yml     # Ambiente local (Python 3.11 + mkdocs serve)
├── contributing.md        # Guia de contribuição
├── README.md              # Esta documentação
└── .github/workflows/     # Pipeline de validação e deploy (GitHub Actions)
```

## Tecnologias

- **MkDocs** (≥ 1.5) e **MkDocs Material** (≥ 9.5) para geração do site.
- **Docker** para padronizar o ambiente local, sem necessidade de instalar Python na máquina.
- **GitHub Actions** para validar o build e publicar no **GitHub Pages**.

## Executando localmente

Com o Docker instalado e rodando, execute na raiz do repositório:

```bash
docker compose up
```

Acesse http://localhost:8000. Para encerrar, pressione `Ctrl + C` no terminal. Detalhes e pré-requisitos em [contributing.md](contributing.md).

## Integração contínua

O workflow em `.github/workflows/deploy.yml` é executado em pushes e pull requests que alteram `docs/`, `mkdocs.yml`, `requirements.txt` ou o próprio workflow:

1. **Validação:** instala as dependências e roda `mkdocs build --strict`, garantindo que o site compila sem erros.
2. **Deploy:** após a validação, as alterações que chegam à branch `main` são publicadas com `mkdocs gh-deploy --force` na branch `gh-pages`, que alimenta o GitHub Pages.

## Como contribuir

O fluxo de trabalho, os padrões de branches e commits e as regras de Pull Request estão descritos no [Guia de Contribuição](contributing.md).
