# Unidade II – Java (Programação Orientada a Objetos)

---

## 1. Visão Geral

Para iniciar em Java, é fundamental que você entenda o paradigma de **Programação Orientada a Objetos (POO)**.

Nesta unidade, você aprenderá conceitos essenciais como classes, objetos, atributos, métodos, construtores e enums, além dos **4 pilares da POO**: Encapsulamento, Herança, Polimorfismo e Abstração.

---

## 2. Fundamentos: Classes e Objetos

Toda classe em Java define a estrutura (atributos) e o comportamento (métodos) de um objeto. O construtor é o método especial responsável por inicializar esses atributos quando um novo objeto é criado.

- Vídeos: Classes, Objetos e Construtores em Java
  - [O que é um Objeto](https://youtu.be/aR7CKNFECx0?si=QtGSP1-JSBU6ZENN)
  - [Criando Classes e Objetos](https://youtu.be/wNaoX6VOj54?si=X_wyz24o2zrsYsXG)

---

## 3. Os 4 Pilares da POO

### 3.1 Encapsulamento

Consiste em proteger os atributos de uma classe, tornando-os privados (`private`) e controlando o acesso a eles por meio de métodos públicos (`getters` e `setters`). Isso evita que outras partes do código alterem o estado do objeto de forma indevida.

- Vídeos: Encapsulamento em Java (get/set, modificadores de acesso)
  - [Visibilidade de um Objeto](https://youtu.be/jFI-qqitzwk?si=xfJrnGxzr0x4d4sE)
  - [Configurando Visibilidade de Atributos e Métodos](https://youtu.be/LV2243j4RTQ?si=eOC_LPkOMHfmCFX9)
  - [Métodos Especiais: Construtores, Getters, Setters](https://youtu.be/g2x9oyBFSco?si=l4kriaEHdxXKjeq_)
  - [Aplicação dos Métodos Especiais](https://youtu.be/6i-_R5cAcEc?si=eFrZDNAobFV7zykJ)
  - [Encapsulamento](https://youtu.be/1wYRGFXpVlg?si=yW0N3s9eCFgEMaji)
  - [Encapsulamento em Java](https://youtu.be/x4JfzV0Wb5w?si=OsMT9SB7hbl_nt6E)

### 3.2 Herança

Permite que uma classe (subclasse) reaproveite atributos e métodos de outra classe (superclasse), usando a palavra-chave `extends`. É útil para modelar relações do tipo "é um" (ex.: `Cachorro` é um `Animal`).

- Vídeos: Herança em Java (extends, super, classes abstratas)
  - [Relacionamento Entre Classes](https://youtu.be/GLHbxDU9iBA?si=IndxRXouNmIgb3io)
  - [Objetos Compostos](https://youtu.be/BfrbCQ3XcrA?si=pK_kRay6KiCRJ_AF)
  - [Relação de Agregação](https://youtu.be/ERdvijGtrq0?si=SB7w-is_-BB0Z0im)
  - [Herança (parte1)](https://youtu.be/_PZldwo0vVo?si=WLzRCIxOpXDhl20m)
  - [Herança (parte 2)](https://youtu.be/19IGAeoFKlU?si=ZqMrFuq3E3QRF4sl)
  - [Herança (parte 3)](https://youtu.be/He887D2WGVw?si=76U7yfUv6y9p1bh-)
  - [Herança (parte 4)](https://youtu.be/5pwV2WdD-_Y?si=Pf2PDPbF0LMQZ80g)

### 3.3 Polimorfismo

Permite que um mesmo método se comporte de forma diferente dependendo do objeto que o executa. Pode acontecer por **sobrescrita** (`@Override`, quando a subclasse redefine um método da superclasse) ou por **sobrecarga** (métodos com mesmo nome, mas parâmetros diferentes).

- Vídeos: Polimorfismo em Java (sobrecarga e sobrescrita)
  - [O que é Polimorfismo](https://youtu.be/9-3-RMEMcq4?si=Hp_cZftE-4fDdUfS)
  - [Polimorfismo em Java](https://youtu.be/NctjqlfKC0U?si=tWtCn1cdXIPspkEE)
  - [Polimorfismo de Sobrecarga](https://youtu.be/hYek1xqWzgs?si=TspNufSJvVJCTkTJ)
  - [Aplicação do Polimorfismo por Sobrecarga](https://youtu.be/b7xGYh3NHZU?si=rtRBqap3h3Q-5F9u)

### 3.4 Abstração

Foca em expor apenas o essencial de um objeto, escondendo detalhes de implementação. Em Java, isso é feito com **classes abstratas** (`abstract`) e **interfaces**, que definem "o quê" uma classe deve fazer, sem necessariamente dizer "como".

- Vídeos: Abstração em Java (classes abstratas e interfaces)
  - [Classes Abstratas](https://youtu.be/ws1NVBGeegs?si=bKLtWxOUWtw4Ngk7)
  - [Métodos Abstratos](https://youtu.be/j97OEyzBcKI?si=M_oQkC4VkCYwpyNX)
  - [Métodos Abstratos: Regras](https://youtu.be/xI0xspht6mA?si=uzCPySAoH1cP47Ev)
  - [Introdução à Interfaces](https://youtu.be/AhVd_DzV3DU?si=MsJunQJ6V0pcmbSJ)
  - [Implementando Múltiplas Interfaces](https://youtu.be/QKjFkaagGdk?si=7ocDnsxGuk9cUnnK)
  - [Atributos e Métodos Estáticos](https://youtu.be/SYyEyR78dSQ?si=DfbTwZIVl2WVXU_D)

### Extras

- [Modificador `static`](https://youtu.be/WBBbsEdzzmA?si=h8nyBbEC0DNipjW7)
- [Métodos Estáticos](https://youtu.be/jowlUssbJmk?si=KFPoy1j8zAED4RE0)
- [Bloco de Inicialização Estático](https://youtu.be/4YE1ewRK-rk?si=f-KXfU6HeHBm-sjy)

---

## 4. Projeto Prático de Consolidação

Agora que você já aprendeu os fundamentos, vamos colocar a mão na massa desenvolvendo o **Gerenciador de Tarefas no Terminal**.

- 🌐 [Instruções do Mini Projeto Parte 1: POO & Terminal ➔](desafio.md#1-mini-projeto-1-poo-terminal-gerenciador-de-tarefas)
