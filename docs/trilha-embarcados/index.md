# Trilha de Eletrônica & Embarcados

---

## 1. Visão Geral e Objetivos

Este documento padroniza a capacitação interna da **Orc'estra Gamificação** para a área de **Eletrônica & Sistemas Embarcados**.

O objetivo é fornecer um caminho de aprendizado prático e orientado por problemas (**PBL — Problem-Based Learning**), capacitando o membro desde os fundamentos de circuitos elétricos e instrumentação de bancada até o desenvolvimento de firmware conectado, protocolos de comunicação, IoT (Internet das Coisas) e design de placas de circuito impresso (**PCB**).

A trilha adota como padrão duas das plataformas de maior destaque na indústria moderna de embarcados:
1. **A Família ESP32 (Espressif)**: Modelos clássicos, série S e série C com núcleos **RISC-V**, rádio Wi-Fi/Bluetooth e grande ecossistema IoT.
2. **A Raspberry Pi Pico 2W (Raspberry Pi Silicon)**: Baseada no novíssimo processador **RP2350** (dual-core ARM Cortex-M33 / dual-core Hazard3 RISC-V), com rádio **CYW43439** (Wi-Fi e Bluetooth) e os inovadores blocos **PIO (Programmable I/O)**.

Como linguagem de programação padrão oficial para prototipagem rápida e didática, adotamos o **MicroPython**, com o suporte de ferramentas como o **Thonny IDE** e o **VS Code + MicroPico**. Para membros que desejam aprofundar em desenvolvimento de baixo nível e alto desempenho, a trilha também disponibiliza implementações equivalentes em **C/C++ (PlatformIO / Pico SDK)**.

Além disso, para garantir que qualquer membro possa participar ativamente mesmo antes de adquirir placas físicas, a trilha integra o simulador online **Wokwi** (com suporte a ESP32 e Raspberry Pi Pico).

---

## Unidades de Aprendizado

<div class="trilhas-grid">
  <a href="unidade-1/" class="trilha-card">
    <h3>Unidade I – Fundamentos de Eletrônica & Instrumentação</h3>
    <p>Grandezas elétricas, Lei de Ohm, componentes passivos/ativos, bancada, multímetro e simulação inicial.</p>
    <span class="trilha-card-link">Acessar Unidade ➔</span>
  </a>

  <a href="unidade-2/" class="trilha-card">
    <h3>Unidade II – ESP32 & Raspberry Pi Pico 2W</h3>
    <p>Arquitetura do ESP32 e do RP2350 (Pico 2W), tensões lógicas de 3.3V, comparativo direto e simulação no Wokwi.</p>
    <span class="trilha-card-link">Acessar Unidade ➔</span>
  </a>

  <a href="unidade-3/" class="trilha-card">
    <h3>Unidade III – Programação MicroPython & Setup</h3>
    <p>Instalação do Thonny e VS Code (MicroPico), módulo machine, time, garbage collection e transição para C/C++.</p>
    <span class="trilha-card-link">Acessar Unidade ➔</span>
  </a>

  <a href="unidade-4/" class="trilha-card">
    <h3>Unidade IV – GPIOs, Interrupções, Sensores & Atuadores</h3>
    <p>Entradas/saídas, PWM, ADC, sensores capacitivos, interrupções (IRQs) e temporização não-bloqueante.</p>
    <span class="trilha-card-link">Acessar Unidade ➔</span>
  </a>

  <a href="unidade-5/" class="trilha-card">
    <h3>Unidade V – Protocolos de Comunicação & IoT</h3>
    <p>Barramentos UART, I2C (display OLED SSD1306), SPI, Wi-Fi com network e telemetria MQTT em tempo real.</p>
    <span class="trilha-card-link">Acessar Unidade ➔</span>
  </a>

  <a href="unidade-6/" class="trilha-card">
    <h3>Unidade VI – PCB no KiCad: Carrier Boards & Shields</h3>
    <p>Esquemáticos elétricos, shields para ESP32 e Raspberry Pi Pico 2W, regras de antena Wi-Fi e geração de Gerbers.</p>
    <span class="trilha-card-link">Acessar Unidade ➔</span>
  </a>
</div>

---

## Central de Projetos Práticos

Concluiu os estudos teóricos das unidades? Acesse a Central de Desafios para desenvolver os projetos práticos de Eletrônica e Sistemas Embarcados (com suporte total a bancada virtual no Wokwi ou hardware físico com ESP32 ou Raspberry Pi Pico 2W):

[Ir para os Desafios Práticos ➔](desafio.md){ .md-button .md-button--primary }
