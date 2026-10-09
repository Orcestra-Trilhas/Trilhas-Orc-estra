# Unidade II – Plataformas Embarcadas: Família ESP32 & Raspberry Pi Pico 2W

---

## 1. Visão Geral

O desenvolvimento de sistemas embarcados modernos é impulsionado por microcontroladores de 32 bits de alta performance, baixo consumo e conectividade sem fio integrada. Na vanguarda dessa transformação estão dois ecossistemas consolidados: a família **ESP32** (da Espressif Systems) e a família **Raspberry Pi Pico** (com destaque para a recém-lançada **Raspberry Pi Pico 2W**, equipada com o processador **RP2350** da Raspberry Pi Silicon).

Nesta unidade, você entenderá a arquitetura e as características elétricas de cada plataforma, comparará seus pontos fortes (**Xtensa vs ARM Cortex-M33 / RISC-V**, periféricos de rede e as inovadoras máquinas de estado **PIO**), e aprenderá a utilizar o **Wokwi** para simular tanto o ESP32 quanto a Raspberry Pi Pico diretamente no navegador em qualquer sistema operacional.

---

## Módulo 2.1: Arquitetura & Características Elétricas Compartilhadas

Antes de conectar qualquer jumper na bancada física ou simulador, é fundamental memorizar a regra elétrica que rege ambas as placas:

> [!CAUTION]
> **Tensão Lógica Estrita de 3.3V (NÃO Tolerante a 5V)**:
> Tanto as portas de GPIO do **ESP32** quanto as do processador **RP2350 (Pico 2W)** operam estritamente em **3.3V** (nível lógico HIGH entre 2.0V e 3.6V). Se um sinal de 5V vindo de sensores ou módulos antigos for aplicado diretamente em seus pinos, **o microcontrolador será queimado irreversivelmente**. Sempre utilize divisores de tensão resistivos ou conversores de nível lógico bidirecionais (*logic level shifters*).

### Alimentação e Pinos Especiais

#### 1. No ESP32 (Série DevKit)
- **Alimentação**:
  - Porta micro-USB / USB-C (5V da porta do computador, rebaixada para 3.3V por um regulador LDO onboard).
  - Pino `VIN` / `5V` (alimentação externa de 5V).
  - Pino `3V3` (alimentação de 3.3V já regulada).
- **Pinos Strapping (GPIO0, GPIO2, GPIO12, GPIO15 no modelo clássico)**:
  - São lidos pelo chip no milissegundo em que é energizado para decidir entre executar o firmware ou entrar em modo de bootloader para gravação. Evite ligar componentes que forcem nível HIGH ou LOW nesses pinos no boot.
- **Pinos Input-Only (GPIO 34, 35, 36, 39)**:
  - Pinos sem transistores de pull-up/pull-down internos, servindo exclusivamente como entradas (ótimos para conversão analógica ADC).

#### 2. Na Raspberry Pi Pico 2W (Processador RP2350)
- **Alimentação**:
  - Porta micro-USB (5V, que passa pelo pino `VSYS` e alimenta o regulador interno RT6154 para gerar 3.3V).
  - Pino `VSYS` (tensão de entrada de 1.8V a 5.5V, ideal para alimentar a placa com 2 a 3 pilhas AA ou baterias LiPo).
  - Pino `3V3(OUT)` (fornece até 300mA regulados a 3.3V para sensores externos).
- **Pino BOOTSEL**:
  - Botão na própria placa que, quando pressionado durante a inserção do cabo USB, monta a Pico como um drive flash virtual USB (*Mass Storage Device*), permitindo gravar firmwares em formato `.uf2` apenas arrastando o arquivo!
- **Pinos ADC Dedicados (GPIO 26, 27 e 28)**:
  - O RP2350 expõe 3 canais analógicos externos (ADC0 no GPIO 26, ADC1 no GPIO 27, ADC2 no GPIO 28) e um canal interno (ADC4) conectado a um sensor de temperatura embutido no próprio silício.
- **LED Onboard Controlado pelo Rádio CYW43439**:
  - Na Pico original, o LED onboard estava no GPIO 25. Na **Pico W** e **Pico 2W**, o LED onboard é conectado aos pinos de GPIO do chip Wi-Fi **Infineon CYW43439**, sendo acessado no MicroPython através do identificador especial `Pin("LED", Pin.OUT)`.

---

## Módulo 2.2: O Guia Comparativo: ESP32 vs Raspberry Pi Pico 2W

Ambas as placas são líderes globais, mas foram projetadas com filosofias de engenharia distintas:

```
┌─────────────────────────────────────────────────────────────────────────┐
│                 ESP32 (Espressif Systems)                   │
│   • Foco: Conectividade IoT nativa no silício principal     │
│   • Rádio Wi-Fi/BT integrado ao chip principal              │
│   • Grande ecossistema de sensores industriais e cloud      │
└─────────────────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────────────────┐
│             Raspberry Pi Pico 2W (RP2350 + CYW43439)        │
│   • Foco: Determinismo de tempo real e E/S flexível (PIO)   │
│   • Arquitetura Dual: ARM Cortex-M33 ou RISC-V Hazard3      │
│   • Rádio Wi-Fi/BT desacoplado via chip auxiliar dedicado   │
└─────────────────────────────────────────────────────────────────────────┘
```

### 1. O Ecossistema ESP32
- **ESP32 Clássico**: Processador Xtensa LX6 Dual-Core a 240 MHz, Wi-Fi 4 e BLE 4.2. Extremamente barato e com enorme acervo de bibliotecas.
- **ESP32-S3**: Processador Xtensa LX7 com aceleração vetorial para inteligência artificial na borda (TinyML), USB OTG nativo e dezenas de GPIOs.
- **ESP32-C3 & C6**: A migração da Espressif para a arquitetura aberta **RISC-V**. O C6 traz suporte de ponta a **Wi-Fi 6**, Bluetooth 5.3 e os protocolos **Matter / Thread**.

### 2. A Raspberry Pi Pico 2W & o Processador RP2350
- **Núcleos Duplos Selecionáveis**: O silício RP2350 traz uma inovação histórica: possui pares de núcleos **ARM Cortex-M33** (com extensões de segurança ARM TrustZone) e pares de núcleos **RISC-V Hazard3** de arquitetura aberta a 150 MHz, permitindo alternar a arquitetura de inicialização.
- **520 KB de SRAM**: Dividida em múltiplos bancos para acesso paralelo sem engarrafamento de barramento.
- **O Módulo Wi-Fi/Bluetooth CYW43439**: Garante conectividade Wi-Fi 802.11 b/g/n (2.4 GHz) e Bluetooth, operando via barramento SPI interno de alta velocidade.
- **O Superpoder do RP2350: PIO (Programmable I/O)**:
  - São 3 blocos de hardware contendo 12 pequenas máquinas de estado programáveis independentes da CPU principal.
  - Permite criar protocolos de comunicação sob medida em hardware puro (ex: saídas VGA, geradores de áudio DVI/I2S, controle de fitas LED WS2812B com temporização ultra-precisa) sem consumir ciclos de processamento dos núcleos principais!

### Tabela Comparativa de Especificações

| Recurso | ESP32 Clássico | ESP32-S3 | Raspberry Pi Pico 2W |
| :--- | :--- | :--- | :--- |
| **CPU Principal** | Xtensa LX6 Dual-Core (240 MHz) | Xtensa LX7 Dual-Core (240 MHz) | **Dual ARM Cortex-M33 ou Dual RISC-V** (150 MHz) |
| **Memória SRAM** | 520 KB | 512 KB | 520 KB |
| **Memória Flash** | Tipicamente 4 MB a 16 MB | 8 MB a 32 MB | 4 MB (QSPI) |
| **Conectividade** | Wi-Fi 4 + BLE 4.2 integrado | Wi-Fi 4 + BLE 5.0 integrado | Wi-Fi 4 + BLE 5.2 (chip Infineon CYW43439) |
| **Periférico Único** | DACs internos, Touch pins capacitivos | Aceleração Vetorial IA (TinyML) | **PIO (12 Máquinas de Estado Programáveis)** |
| **Gravação de Firmware** | Serial UART via Bootloader (`esptool`) | Serial UART ou USB Nativo | **Arrastar arquivo `.uf2` via USB (BOOTSEL)** |
| **Simulação Wokwi** | Suporte total (com Wi-Fi virtual) | Suporte total | Suporte total (Pico / Pico W com Wi-Fi virtual) |

### Recomendações Práticas para a Orc'estra
- **Escolha o ESP32**: Para projetos focados em sensores ambientais remotos, servidores web leves embarcados, IoT com MQTT massivo e aplicações onde o rádio é o coração do projeto.
- **Escolha a Raspberry Pi Pico 2W**: Para projetos de controle em tempo real determinístico, periféricos complexos de alta velocidade (como displays gráficos, geração de sinais customizados via PIO), emulação de periféricos USB e segurança criptográfica via TrustZone.

**Links:**

- 🎥 [Vídeo: Todos os ESP32 Comparados! Veja Qual Escolher (Fabio Souza)](https://www.youtube.com/watch?v=rbv5BoBSGus)
- 🎥 [Vídeo: Conheça a placa ESP32-S3-DEVKITC-1 (Fabio Souza)](https://www.youtube.com/watch?v=cB2O9Xh7c54)
- 🌐 [Raspberry Pi: Datasheet Oficial do Processador RP2350](https://datasheets.raspberrypi.com/rp2350/rp2350-datasheet.pdf)
- 🌐 [Espressif: ESP Product Selector](https://products.espressif.com/)

---

## Módulo 2.3: Bancada Virtual & Simulação com Wokwi

Não possui uma placa física em mãos? Você não precisa esperar: é possível desenvolver, programar e testar 100% dos projetos da trilha usando o simulador **Wokwi** diretamente no navegador:

### 1. Wokwi para ESP32
- Permite carregar firmware MicroPython ou binários C/C++.
- Suporta componentes analógicos e digitais (LEDs, botões, potenciômetros, DHT22, relés, telas OLED I2C).
- **Wi-Fi Simulado**: Através do gateway virtual `Wokwi-GUEST`, o ESP32 virtual conecta-se à internet real, permitindo fazer requisições HTTP e enviar telemetria para brokers MQTT externos!
- 🌐 [Acessar Simulador Wokwi ESP32](https://wokwi.com/esp32)

### 2. Wokwi para Raspberry Pi Pico & Pico W
- Suporta a placa Raspberry Pi Pico e Raspberry Pi Pico W executando MicroPython nativo.
- Permite ligar sensores aos pinos do RP2040/RP2350 e depurar código Python com saída imediata no terminal serial interativo (REPL).
- **Depurador Visual de PIO**: O Wokwi inclui um inspetor gráfico interativo para depurar ciclo a ciclo o conteúdo dos registradores `OSR`, `ISR` e instruções das máquinas de estado **PIO (*Programmable I/O*)**.
- 🌐 [Acessar Simulador Wokwi Pi Pico](https://wokwi.com/pi-pico)

### 3. Emulação Avançada Multi-Nó: Renode
Para cenários onde é necessário simular redes inteiras de dispositivos ou rodar testes automatizados de firmware em pipelines de CI/CD:
- O **Renode** (da Antmicro) é um framework de simulação de nível de instrução que permite criar topologias virtuais com múltiplos microcontroladores (ESP32, RP2040/RP2350 e sensores) conectados a redes Ethernet/Wi-Fi virtuais e integrados ao depurador GDB e Wireshark.
- 🌐 [Acessar Documentação Oficial do Renode](https://renode.io/)

---

## Desafios Práticos da Unidade II

!!! example "[Exercício 1] Piscar o LED Onboard no Wokwi (ESP32)"
    No simulador [Wokwi ESP32](https://wokwi.com/esp32), crie um novo projeto com **MicroPython**:
    
    1. Crie o arquivo `main.py` com o seguinte código:
        ```python
        import machine
        import time

        led = machine.Pin(2, machine.Pin.OUT) # LED onboard no GPIO 2

        while True:
            led.value(not led.value())
            time.sleep(0.5)
        ```
    2. Clique em **Play** e observe o LED piscar a 1 Hz no simulador.

!!! example "[Exercício 2] Piscar o LED Onboard no Wokwi (Raspberry Pi Pico)"
    No simulador [Wokwi Pi Pico](https://wokwi.com/pi-pico), crie um novo projeto **Raspberry Pi Pico (MicroPython)**:
    
    1. Crie o arquivo `main.py` com o seguinte código:
        ```python
        import machine
        import time

        # Na Pico convencional usa-se o pino 25; na Pico W/2W usa-se "LED"
        led = machine.Pin("LED", machine.Pin.OUT)

        while True:
            led.toggle()
            time.sleep(0.5)
        ```
    2. Inicie a simulação e confira o funcionamento idêntico entre os ecossistemas!
