# Unidade I – Preparando o Ambiente

---

## 1. Visão Geral

Antes de escrever código em Java, você precisa de um ambiente de desenvolvimento configurado e funcionando sem dores de cabeça.

Nesta unidade, você irá escolher e instalar a IDE (Integrated Development Environment) adequada para o desenvolvimento de aplicações Java modernas.

---

## 2. Ferramentas & IDEs

### 1.1 IntelliJ IDEA (Recomendado)
O **IntelliJ IDEA (Community Edition)** é o padrão de mercado para desenvolvimento Java. Ele oferece o melhor suporte a autocompletar, refatoração automática, geração de código e integração nativa com Spring Boot.

#### Windows

- [Vídeo da Instalação](https://www.youtube.com/watch?v=kNP32bOVNJo)

#### Linux

- [Vídeo da Instalação](https://www.youtube.com/watch?v=YKHM_DUOV0k)

### 1.2 VS Code (Opção Leve)
Se você já utiliza o **Visual Studio Code**, pode utilizá-lo instalando o pacote oficial de extensões Java da Microsoft.

Se você ainda não tem o VS Code instalado, segue o vídeo para te ajudar na instalação

- [Vídeo: Instalação do Java no Windows e configuração no VS Code](https://www.youtube.com/watch?v=7d4KjemMQpk)

### 1.3 Instalando o JDK no Linux

Independentemente da IDE escolhida, você precisa ter o JDK instalado no sistema. Abaixo estão os comandos para instalar o OpenJDK nas distribuições mais comuns (o exemplo usa a versão 21, mas você pode ajustar o número conforme a versão desejada):

#### Ubuntu/Debian (apt)

```bash
sudo apt update
sudo apt install openjdk-21-jdk
```

#### Fedora / RHEL / CentOS (dnf)

```bash
sudo dnf install java-21-openjdk-devel
```

#### Arch Linux / Manjaro (pacman)

```bash
sudo pacman -S jdk-openjdk
```

#### openSUSE (zypper)

```bash
sudo zypper install java-21-openjdk-devel
```

#### Pós Instalação

verifique se o Java foi reconhecido corretamente:

```bash
java -version
javac -version
```

Se os comandos retornarem a versão instalada, o ambiente está pronto para uso com a IDE de sua preferência.

---

## 3. Próximo Passo

Com o ambiente de desenvolvimento instalado, avance para a **Unidade II** para aprender os fundamentos da linguagem Java e Orientação a Objetos.
