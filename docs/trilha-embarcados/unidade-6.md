# Unidade VI – Design de Circuitos & PCB no KiCad: Carrier Boards & Shields

---

## 1. Visão Geral

Montar circuitos na protoboard é ideal para validar ideias rápidas. Porém, fios soltos geram mau contato, ruído eletromagnético e impossibilitam o uso do projeto em ambientes reais ou na entrega de soluções finais para clientes da **Orc'estra Gamificação**.

Nesta unidade, você aprenderá a projetar uma **Placa de Circuito Impresso (PCB — Printed Circuit Board)** profissional utilizando o **KiCad**, a suíte de automação de design eletrônico (EDA) livre e multiplataforma mais utilizada no mundo. Você aprenderá a desenhar esquemáticos, rotear trilhas com regras de alta frequência para o **ESP32** e para a **Raspberry Pi Pico 2W**, e gerar arquivos **Gerber** prontos para fabricação industrial.

---

## Módulo 6.1: O Esquemático Elétrico & Shields / Carrier Boards

O esquemático elétrico é o mapa lógico do circuito, onde os componentes são representados por símbolos padronizados e conectados por condutores elétricos (*nets*).

### 1. Projetando Shields vs. Placas Carrier
- **Shield para ESP32 DevKit**: Utiliza duas barras de pinos fêmea de passo 2.54mm separadas pela largura do DevKit, permitindo plugar e desdobrar sensores, relés e conectores sem soldar o microcontrolador permanentemente.
- **Carrier Board para Raspberry Pi Pico 2W**: A Pico 2W possui uma característica de fabricação fantástica: suas bordas possuem **orifícios castelados (*castellated holes*)**. Isso significa que ela pode ser tanto encaixada em soquetes com barras de pinos macho/fêmea quanto **soldada diretamente na superfície (SMD)** da sua placa de circuito impresso como se fosse um módulo integrado!

### 2. O Circuito Mínimo de Alimentação & Proteção
1. **Regulador LDO de 3.3V**: Tanto o ESP32 quanto o chip Wi-Fi da Pico 2W demandam picos transitórios de corrente ao transmitir sinais de rádio (até 500mA no ESP32 e ~300mA na Pico W/2W). Um bom regulador LDO (ex: AMS1117-3.3 ou ME6211) alimentado pelo conector de 5V ou bateria externa garante estabilidade.
2. **Capacitores de Desacoplamento**: Capacitores cerâmicos de **100nF** devem ficar posicionados o mais próximo possível das linhas de alimentação dos conectores e sensores, filtrando ruídos de alta frequência.

### 3. Validação Prévia com Simulação SPICE no KiCad (ngspice)
Antes de partir para o desenho das trilhas e fabricação da placa, o KiCad possui um poderoso motor de simulação **SPICE (ngspice)** integrado diretamente ao editor de esquemáticos (**menu Inspecionar > Simulador**):
- **O que simular**:
  - Divisores resistivos de tensão para garantir que sinais nunca ultrapassem os limites de 3.3V do ESP32 ou da Pico 2W.
  - Curvas de chaveamento de transistores MOSFET acionando relés com diodo de flyback.
  - Filtros passa-baixas (RC) para sinais analógicos e atenuação de ruído na linha de alimentação.
- Permite plotar gráficos de regime transitório (tensão e corrente ao longo do tempo) e resposta em frequência (diagramas de Bode) com precisão industrial antes de soldar qualquer componente.

**Links:**

- 🎥 [Vídeo: KiCad — Diagramas Esquemáticos e PCB (WR Kits)](https://www.youtube.com/watch?v=NEkV3ylIeKQ)
- 🎥 [Vídeo: Desenhando Esquemáticos no KiCad sem Erros (Sob Controle)](https://www.youtube.com/watch?v=IlxMAqR6Le4)
- 🎥 [Vídeo: Novo Esquemático no KiCad ao Vivo (WR Kits)](https://www.youtube.com/watch?v=qADFkoizw1I)
- 📖 [Documentação Oficial do KiCad: Simulador SPICE Integrado](https://docs.kicad.org/8.0/en/eeschema/eeschema.html#simulator)
- 🌐 [Download Oficial do KiCad EDA (Livre & Open-Source)](https://www.kicad.org/download/)
- 🌐 [Raspberry Pi: Guia de Design de Hardware com RP2350](https://datasheets.raspberrypi.com/rp2350/hardware-design-with-rp2350.pdf)

---

## Módulo 6.2: Layout da Placa & Boas Práticas de Roteamento

Após finalizar o esquemático e associar as pegadas físicas (*footprints*), o projeto é transferido para o editor de PCB do KiCad.

### A Regra de Ouro das Antenas Wi-Fi (ESP32 & Pico 2W)
Tanto o ESP32 quanto a Pico 2W possuem antenas integradas de 2.4 GHz na ponta de suas placas:

> [!CAUTION]
> **Zona Livre de Cobre (Keepout Zone)**:
> A região da antena deve ficar posicionada projetada para fora da borda da sua placa carrier ou sobre uma área livre de cobre (*Keepout Area*). **Nunca** passe trilhas, furos, planos de terra ou qualquer camada de cobre logo abaixo ou ao redor da antena nas camadas da PCB. Se houver cobre sob a antena, as ondas de rádio serão refletidas e o alcance do Wi-Fi cairá drasticamente!

### Recomendações de Roteamento (Routing)
- **Largura das Trilhas**:
  - Trilhas de sinais lógicos comuns (GPIOs, SPI, I2C): ~0.25 mm a 0.3 mm.
  - Trilhas de alimentação (3.3V e 5V/VIN): mais largas (0.6 mm a 1.0 mm) para minimizar queda de tensão e resistência parasita.
- **Plano de Terra Contínuo (Copper Pour / GND Zone)**:
  - Preencha toda a camada inferior (*B.Cu*) com uma malha de GND contínua. Isso reduz o ruído de alta frequência e fornece um caminho de retorno de corrente de baixa impedância.
- **Visualizador 3D**: O KiCad possui um renderizador 3D em tempo real (`Alt + 3`) que permite inspecionar o alinhamento de conectores, parafusos de fixação e a ergonomia final da placa.

**Links:**

- 🎥 [Vídeo: Tutorial Rápido sobre KiCad — Projeto de Layout de PCB (Rodrigo Varella Tambara)](https://www.youtube.com/watch?v=RL3nTATgOFg)
- 🎥 [Vídeo: Como Criar Placas Profissionais no KiCad com Modelo 3D (E2T Automação)](https://www.youtube.com/watch?v=biM2VLC0nsE)

---

## Módulo 6.3: Validação (DRC) & Geração de Arquivos Gerber

Antes de enviar sua placa para uma fabricante industrial (como JLCPCB, PCBWay ou OSH Park), ela precisa passar por verificações rigorosas:

### 1. DRC (Design Rules Check)
O verificador de regras de design analisa se o seu projeto respeita as capacidades mecânicas e elétricas da fábrica:
- Espaçamento mínimo entre trilhas (*Clearance*): tipicamente **≥ 0.15 mm** (6 mil).
- Diâmetro mínimo de furação (*Drill size*): tipicamente **≥ 0.3 mm**.
- Trilhas desconectadas ou curto-circuitos acidentais.

### 2. Exportação dos Arquivos Gerber & Drill
O maquinário industrial de fabricação utiliza o padrão industrial **Gerber (RS-274X)**:
- Camadas de cobre superior e inferior (`.GTL`, `.GBL`).
- Máscara de solda (*Solder Mask* — a camada protetora colorida).
- Serigrafia (*Silkscreen* — os textos e desenhos brancos que identificam os pinos).
- Contorno da placa (*Edge.Cuts*).
- Arquivo de furação CNC (`.DRL`).
- Todos os arquivos são compactados em um único arquivo `.zip` para envio direto à fábrica.

---

## Desafios Práticos da Unidade VI

!!! example "[Exercício] Minha Primeira Placa Carrier / Shield no KiCad"
    No KiCad, projete uma placa de circuito impresso do tipo *Carrier Board / Shield* para a placa de sua preferência (**ESP32 DevKit** ou **Raspberry Pi Pico 2W**):
    
    1. Crie o esquemático com duas barras de pinos fêmea para encaixar a placa de desenvolvimento.
    2. Adicione os conectores: 1 barra de 4 pinos para display OLED I2C (VCC, GND, SCL, SDA), 1 conector para sensor DHT22 e 1 saída para acionamento de relé com transistor MOSFET e diodo flyback.
    3. No editor de PCB, posicione os componentes respeitando a zona livre da antena, desenhe o contorno da placa (`Edge.Cuts`), trace as trilhas e adicione um plano de terra contínuo no fundo.
    4. Execute o **DRC** sem nenhum erro e visualize a placa finalizada no renderizador 3D (`Alt + 3`).

---

## Próximo Passo

Parabéns por completar todas as 6 unidades de aprendizado da Trilha de Eletrônica & Embarcados! Você possui agora a base completa para programar em MicroPython, integrar periféricos IoT e criar hardware profissional.

Avance para a **Central de Desafios Práticos** para escolher e desenvolver o seu projeto de conclusão da trilha.

[Ir para a Central de Desafios Práticos ➔](desafio.md){ .md-button .md-button--primary }
