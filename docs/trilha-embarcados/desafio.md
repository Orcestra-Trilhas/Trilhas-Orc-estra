# Central de Desafios Práticos – Eletrônica & Embarcados

---

## 1. Visão Geral

A metodologia de capacitação da **Orc'estra Gamificação** é fundamentada no **PBL (Problem-Based Learning)**. A teoria só se consolida quando colocada à prova na resolução de problemas reais de engenharia.

Esta Central de Desafios apresenta 3 projetos práticos progressivos. **Todos os projetos podem ser desenvolvidos e validados 100% no simulador Wokwi (sem necessidade de adquirir hardware físico) ou implementados fisicamente na bancada utilizando uma placa ESP32 ou uma Raspberry Pi Pico 2W.**

---

## Desafio Nível 1 (Iniciante) – Sinalizador Interativo com Sensor de Toque & PWM

Construir um sinalizador visual inteligente que reage à aproximação humana e ao ambiente, utilizando saídas analógicas virtuais (PWM) e entradas sensíveis ao toque.

### Requisitos:
- [ ] **Plataforma**: Utilizar um **ESP32** ou uma **Raspberry Pi Pico 2W** (física ou simulada no Wokwi com MicroPython).
- [ ] **Atuação Visual**: Utilizar um LED RGB (ou 3 LEDs independentes: Vermelho, Amarelo e Verde).
- [ ] **Controle de Brilho**: Implementar controle de brilho via **PWM (`duty_u16`)**, criando transições suaves de cor (*fading*).
- [ ] **Gatilho de Interação**:
  - No **ESP32**: Utilizar pelo menos um **Pino de Toque Capacitivo (`TouchPad`)** interno.
  - Na **Raspberry Pi Pico 2W**: Utilizar um sensor capacitivo externo (ex: **TTP223**) ou botão tátil com pull-up.
  - O toque/botão deve alternar entre 3 modos de operação (Modo Manual, Modo Automático e Modo Alerta).
- [ ] **Temporização Não-Bloqueante**: Toda a temporização do sinalizador deve ser construída com `time.ticks_ms()` e `time.ticks_diff()` (**proibido o uso de `time.sleep()` no loop principal**).

---

## Desafio Nível 2 (Intermediário) – Estação de Monitoramento Ambiental com Display OLED I2C

Projetar uma central meteorológica de bancada que coleta dados de múltiplos sensores analógicos/digitais e exibe métricas e gráficos em uma interface gráfica local.

### Requisitos:
- [ ] **Plataforma**: ESP32 ou Raspberry Pi Pico 2W (física ou no Wokwi).
- [ ] **Sensoriamento**:
  - Sensor de temperatura e umidade digital (**DHT22**).
  - Sensor de luminosidade analógico (**LDR** em divisor de tensão lido via `ADC`).
- [ ] **Interface Gráfica Local**:
  - Integrar um display **OLED 0.96" SSD1306 (128x64)** comunicando-se via barramento **I2C**.
  - Exibir valores numéricos formatados (°C, % e nível de luz).
  - Desenhar uma barra gráfica animada indicando a intensidade de luz ou conforto térmico.
- [ ] **Interrupção de Hardware (IRQ)**: Implementar um botão físico com **interrupção externa (`irq`)** para alternar entre diferentes telas de visualização (Tela 1: Temperatura/Umidade, Tela 2: Luminosidade/Status do Sistema).

---

## Desafio Nível 3 (Avançado / Projeto Final) – Dispositivo IoT Completo com Telemetria MQTT & Dashboard

Desenvolver uma solução IoT completa de ponta a ponta: do sensor no hardware ao controle remoto em tempo real através da internet.

### Requisitos:
- [ ] **Firmware Conectado**: O microcontrolador (ESP32 ou Pico 2W) deve se conectar à rede Wi-Fi através do módulo `network` do MicroPython, com rotina de reconexão automática em caso de queda de sinal.
- [ ] **Protocolo MQTT**:
  - Conectar-se a um Broker MQTT (como Adafruit IO, HiveMQ Cloud ou broker local) usando a biblioteca `umqtt.simple`.
  - Publicar a cada 5 segundos os dados de telemetria em tópicos dedicados (ex: `orcestra/dispositivo1/sensores`).
  - Subscrever a um tópico de comando (ex: `orcestra/dispositivo1/atuador/set`) para receber ordens da nuvem.
- [ ] **Atuação Física e Proteção**: Acionar um relé (ou transistor com carga) imediatamente ao receber uma mensagem MQTT da nuvem.
- [ ] **Dashboard em Nuvem**: Criar um painel visual (no **Adafruit IO**, **Blynk** ou **HiveMQ Web Client**) contendo:
  - Gráfico histórico da temperatura/umidade em tempo real.
  - Um botão liga/desliga para controlar o atuador remoto com retorno de confirmação.
- [ ] **Carrier Board / Shield (Bônus)**: Desenho do esquemático e layout da placa correspondente no **KiCad**.

---

## Critérios de Avaliação

Para obter a aprovação na capacitação, submeta o projeto em um repositório no GitHub contendo:

- [ ] **Código Fonte Modular**: Projeto estruturado com scripts MicroPython limpos (`boot.py` e `main.py`), sem rotinas bloqueantes no loop e com boas práticas de nomenclatura.
- [ ] **README Completo**:
  - Descrição detalhada do projeto e objetivo.
  - Diagrama de ligações elétricas (pinagem utilizada no ESP32 ou Raspberry Pi Pico 2W).
  - **Link público do projeto no Wokwi** (permitindo rodar a simulação com 1 clique no navegador) ou fotos/vídeo demonstrativo da montagem física na bancada.
  - Instruções de configuração do Wi-Fi e tópicos MQTT.

---

## Recursos Complementares Gratuitos

Cursos, simuladores e referências essenciais para consulta e aprofundamento:

| Recurso | Tipo | Link |
| :--- | :--- | :--- |
| Wokwi ESP32 Simulator | Simulador Online (Navegador) | [Acessar](https://wokwi.com/esp32) |
| Wokwi Raspberry Pi Pico Simulator | Simulador Online (Navegador) | [Acessar](https://wokwi.com/pi-pico) |
| Documentação Oficial MicroPython | Referência Técnica | [Acessar](https://docs.micropython.org/en/latest/) |
| Thonny IDE Oficial | Software Livre | [Acessar](https://thonny.org/) |
| Datasheet Oficial do RP2350 (Pico 2) | Documentação de Hardware | [Acessar](https://datasheets.raspberrypi.com/rp2350/rp2350-datasheet.pdf) |
| Espressif ESP-IDF Documentation | Documentação Oficial | [Acessar](https://docs.espressif.com/projects/esp-idf/en/latest/) |
| Todos os ESP32 Comparados (Fabio Souza) | Vídeo | [Assistir](https://www.youtube.com/watch?v=rbv5BoBSGus) |
| KiCad — Diagramas Esquemáticos e PCB (WR Kits) | Vídeo | [Assistir](https://www.youtube.com/watch?v=NEkV3ylIeKQ) |
| Download Oficial do KiCad EDA | Software Livre | [Acessar](https://www.kicad.org/download/) |
| Simulador SPICE no KiCad (ngspice) | Documentação Oficial | [Acessar](https://docs.kicad.org/8.0/en/eeschema/eeschema.html#simulator) |
| PulseView / sigrok (Análise Lógica) | Software Livre | [Acessar](https://sigrok.org/wiki/PulseView) |

---

[← Voltar para a Visão Geral da Trilha de Eletrônica & Embarcados](index.md){ .md-button }
