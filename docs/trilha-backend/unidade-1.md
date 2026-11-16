# Unidade I: Preparando o Ambiente

---

## 1. Visão Geral

Antes de escrever código em Java, você precisa de um ambiente funcionando: JDK, IDE, uma ferramenta de build (Maven) e o Spring Initializr para gerar projetos.

Ao final desta unidade, você terá um projeto Spring Boot criado, compilando e rodando na sua máquina.

---

## 2. Ferramentas & IDEs

### 1.1 IntelliJ IDEA (Recomendado)

O **IntelliJ IDEA** é o padrão de mercado para Java: bom autocompletar, refatoração automática e geração de código.

!!! note "Edições do IntelliJ"
    A JetBrains alterou a forma de distribuir as edições gratuita e paga nos últimos anos. Antes de instalar, confira no site oficial quais recursos de Spring estão disponíveis na edição gratuita. Se algum recurso de Spring não estiver incluído, tudo o que a trilha pede continua funcionando: o que muda é só o conforto da IDE (por exemplo, assistentes de configuração).

#### Windows

- [Vídeo da Instalação](https://www.youtube.com/watch?v=kNP32bOVNJo)

#### Linux

- [Vídeo da Instalação](https://www.youtube.com/watch?v=YKHM_DUOV0k)

### 1.2 VS Code (Opção Leve)

Se você já utiliza o **Visual Studio Code**, instale o pacote oficial de extensões Java da Microsoft (*Extension Pack for Java*) e a extensão *Spring Boot Extension Pack*.

- [Vídeo: Instalação do Java no Windows e configuração no VS Code](https://www.youtube.com/watch?v=7d4KjemMQpk)

---

## 3. Instalando o JDK

O **JDK** (Java Development Kit) é obrigatório independentemente da IDE. Use a versão **21** (LTS). O Spring Boot 3 exige no mínimo Java 17.

### 1.3 Windows

Opção 1: baixe o instalador do **Eclipse Temurin 21** em [adoptium.net](https://adoptium.net) e marque a opção de configurar o `JAVA_HOME` durante a instalação.

Opção 2: pelo terminal (PowerShell):

```powershell
winget install EclipseAdoptium.Temurin.21.JDK
```

### 1.4 macOS

```bash
brew install openjdk@21
sudo ln -sfn $(brew --prefix openjdk@21)/libexec/openjdk.jdk /Library/Java/JavaVirtualMachines/openjdk-21.jdk
```

### 1.5 Linux

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

### 1.6 Alternativa para macOS e Linux: SDKMAN

O [SDKMAN](https://sdkman.io) instala e alterna entre versões de Java sem mexer no sistema. Útil quando projetos diferentes exigem JDKs diferentes.

### 1.7 Verificando a instalação

```bash
java -version
javac -version
```

Os dois comandos devem mostrar a versão 21. Se `javac` não for reconhecido, você instalou só o JRE (sem o compilador) ou o `PATH`/`JAVA_HOME` não está configurado.

---

## 4. Maven: Build e Dependências

Projetos Java reais não são só arquivos `.java` soltos. Uma **ferramenta de build** compila, baixa dependências, roda testes e empacota a aplicação. Nesta trilha usamos o **Maven**.

### 1.8 O `pom.xml`

É o arquivo que descreve o projeto. Os três blocos que você mais vai ler:

```xml
<parent>   <!-- herda versões testadas do Spring Boot -->
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>3.4.0</version>
</parent>

<properties>
    <java.version>21</java.version>
</properties>

<dependencies>   <!-- bibliotecas que o projeto usa -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
</dependencies>
```

Repare que a dependência acima **não tem versão**: ela é gerenciada pelo `parent`. É isso que evita conflito entre bibliotecas.

### 1.9 Comandos essenciais

Todo projeto gerado pelo Spring Initializr traz o **Maven Wrapper** (`mvnw` / `mvnw.cmd`), então você não precisa instalar o Maven:

```bash
./mvnw spring-boot:run     # sobe a aplicação  (Windows: mvnw.cmd spring-boot:run)
./mvnw test                # roda os testes
./mvnw clean package       # compila, testa e gera o .jar em target/
```

!!! tip "Gradle"
    Existe também o Gradle, outra ferramenta de build muito usada. A trilha usa Maven; se algum projeto da empresa usar Gradle, os conceitos (dependências, tarefas de build) são os mesmos.

---

## 5. Spring Initializr

O [Spring Initializr](https://start.spring.io) (e o assistente equivalente dentro das IDEs) gera o esqueleto de um projeto Spring Boot. Você escolhe:

- **Project:** Maven
- **Language:** Java
- **Spring Boot:** versão 3.x estável (evite versões marcadas como SNAPSHOT ou M1/RC)
- **Packaging:** Jar
- **Java:** 21
- **Dependencies:** as que o projeto precisar (ex.: *Spring Web*)

Depois de gerar, abra o projeto na IDE e execute a classe com `@SpringBootApplication`. Se aparecer no console a mensagem de que o Tomcat iniciou na porta 8080, o ambiente está pronto.

---

## 6. Checklist de Conclusão

- [ ] `java -version` e `javac -version` mostram a versão 21.
- [ ] Sei dizer para que servem o `pom.xml` e o Maven Wrapper.
- [ ] Gerei um projeto no Spring Initializr, abri na IDE e ele sobe sem erro.
- [ ] Acesso `http://localhost:8080` e recebo uma resposta (um erro 404 da página padrão é esperado e conta como sucesso: significa que o servidor subiu).

---

## 7. Atividade Prática

- 🌐 [Atividade 0: Ambiente ➔](desafio.md#atividade-0)

---

## 8. Próximo Passo

Avance para a **Unidade II** para aprender os fundamentos da linguagem Java e Orientação a Objetos.
