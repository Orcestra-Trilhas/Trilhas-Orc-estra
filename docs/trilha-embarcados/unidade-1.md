# Unidade I – Fundamentos de Eletrônica & Instrumentação

---

## 1. Visão Geral

Antes de programar microcontroladores e conectar periféricos, é essencial compreender a física dos circuitos eletrônicos. No mundo dos sistemas embarcados, uma ligação incorreta ou o esquecimento de um resistor de proteção pode danificar permanentemente o seu microcontrolador.

Nesta unidade, você aprenderá as grandezas elétricas essenciais, o funcionamento dos componentes passivos e ativos mais comuns, o uso do multímetro e como montar circuitos tanto na bancada física quanto em simuladores virtuais.

---

## Módulo 1.1: Grandezas Elétricas & Leis Fundamentais

Compreender o comportamento da energia elétrica em um circuito fechado.

**Conceitos-Chave:**

- **Tensão Elétrica (`V` ou `U`, em Volts)**: Diferença de potencial elétrico entre dois pontos. É a "força" que impulsiona os elétrons.
- **Corrente Elétrica (`I`, em Amperes)**: Fluxo ordenado de elétrons através de um condutor por unidade de tempo.
- **Resistência Elétrica (`R`, em Ohms `Ω`)**: Oposição oferecida por um material à passagem da corrente elétrica.
- **Potência Elétrica (`P`, em Watts)**: Taxa de conversão de energia elétrica em trabalho ou calor (`P = V × I`).
- **Primeira Lei de Ohm**: A relação fundamental entre tensão, resistência e corrente:

    ```text
    V = R × I    (Tensão = Resistência × Corrente)
    I = V / R    (Corrente = Tensão / Resistência)
    R = V / I    (Resistência = Tensão / Corrente)
    ```

- **Leis de Kirchhoff**:
  - *Lei das Correntes (LKC)*: A soma das correntes que entram em um nó é igual à soma das correntes que saem.
  - *Lei das Tensões (LKV)*: A soma algébrica das diferenças de potencial em qualquer malha fechada é igual a zero.

**Links:**

- 🎥 [Vídeo: Lei de Ohm & Termos Equivocados (WR Kits)](https://www.youtube.com/watch?v=DwM56u-OiRE)
- 🎥 [Vídeo: Para que servem os componentes eletrônicos? (Manual do Mundo)](https://www.youtube.com/watch?v=C54Cp819Ebc)
- 🌐 [Simulador Interativo Falstad Circuit Simulator (Navegador)](https://www.falstad.com/circuit/)

---

## Módulo 1.2: Componentes Eletrônicos Fundamentais

Conhecer os blocos construtivos essenciais de qualquer hardware eletrônico.

**Conceitos-Chave:**

- **Resistores**: Limitam a corrente e dividem tensões.
  - **Como dimensionar o resistor de proteção de um LED**:
    ```text
    R = (V_fonte - V_led) / I_led
    ```
    *Exemplo prático: Para alimentar um LED vermelho (`V_led ≈ 2.0V`, `I_led ≈ 15mA` ou `0.015A`) com a saída lógica de `3.3V` de um ESP32 ou Raspberry Pi Pico 2W:*
    ```text
    R = (3.3V - 2.0V) / 0.015A = 1.3 / 0.015 = 86.6 Ω
    ```
    *Na prática, arredonda-se para o valor comercial superior mais comum: **100 Ω** ou **220 Ω**.*
  - Resistores de **Pull-Up** e **Pull-Down**: Garantem um nível lógico bem definido (HIGH ou LOW) em pinos de entrada quando uma chave está aberta, evitando estados flutuantes (*floating*).
- **Capacitores**: Armazenam carga elétrica em um campo eletrostático.
  - Aplicações principais em embarcados: desacoplamento de ruído de alta frequência (capacitores cerâmicos de 100nF próximos aos pinos de alimentação) e filtragem de fontes (capacitores eletrolíticos de 10µF a 100µF).
- **Diodos & LEDs**: Permitem a passagem de corrente em apenas um sentido (polarização direta).
  - Diodo em antiparalelo (*flyback/freewheeling*): Obrigatório ao acionar relés ou motores para drenar picos indutivos gerados pela bobina e proteger os pinos do microcontrolador.
- **Transistores (BJT e MOSFET)**: Funcionam como amplificadores ou chaves eletrônicas (*switches*).
  - Como o ESP32 e a Raspberry Pi Pico 2W fornecem apenas ~4mA a ~12mA por pino a 3.3V, transistores MOSFET (como o 2N7000 ou IRLZ44N) ou BJTs (como o BC547) são fundamentais para acionar cargas de maior potência (buzzers, relés, motores e fitas de LED).

**Links:**

- 🎥 [Vídeo: Como Funcionam os Componentes Mais Importantes da Eletrônica (Inetec)](https://www.youtube.com/watch?v=a-l4tGfis0Q)
- 🎥 [Vídeo: Como Soldar Componentes Eletrônicos (Brincando com Ideias)](https://www.youtube.com/watch?v=KrUbLwjmwDY)
- 📖 [Guia Didático SparkFun: Pull-up Resistors](https://learn.sparkfun.com/tutorials/pull-up-resistors)

---

## Módulo 1.3: Instrumentação de Bancada & Simulação

Aprender a medir grandezas elétricas e depurar circuitos com segurança.

**Conceitos-Chave:**

- **Multímetro Digital**:
  - **Medição de Tensão (Voltímetro)**: Sempre em **paralelo** com o componente/fonte sob medição.
  - **Medição de Corrente (Amperímetro)**: Sempre em **série** (o circuito deve ser aberto para que a corrente passe por dentro do instrumento). *Atenção: nunca meça corrente em paralelo com a fonte, pois isso causa curto-circuito!*
  - **Medição de Resistência / Continuidade**: Mede-se com o circuito **desenergizado** (desconectado da fonte).
- **Protoboard (Matriz de Contatos)**:
  - Barramentos longitudinais (+ e -) para distribuição de alimentação.
  - Colunas centrais de 5 furos conectadas eletricamente em linhas perpendiculares.
- **Simulação Virtual de Bancada**:
  - Para quem está começando sem equipamentos físicos, o **Tinkercad Circuits** e o **Wokwi** oferecem bancada virtual com protoboard, multímetro e componentes realistas.

**Links:**

- 🎥 [Vídeo: Primeiros Passos com Multímetro — Medindo Tensão e Corrente DC (WR Kits)](https://www.youtube.com/watch?v=6Adq-hAqvEQ)
- 🎥 [Vídeo: Primeiros Passos com Multímetro — Medindo Resistores e Continuidade (WR Kits)](https://www.youtube.com/watch?v=ORp4Fb3zCJw)
- 🌐 [Tinkercad Circuits — Simulador Gratuito](https://www.tinkercad.com/circuits)

---

## Desafios Práticos da Unidade I

!!! example "[Exercício 1] Dimensionamento e Montagem de LED"
    Calcule o resistor ideal para alimentar um LED azul (`V_led = 3.0V`, `I_led = 10mA` ou `0.01A`) ligado a uma fonte de `5V`:
    
    1. Calcule a resistência necessária (`R = (V_fonte - V_led) / I`) e a potência dissipada pelo resistor (`P = I² × R`).
    2. Monte o circuito no **Tinkercad Circuits** ou na protoboard física.
    3. Meça a tensão sobre o LED e a corrente no circuito usando o multímetro virtual.

!!! example "[Exercício 2] Divisor de Tensão Resistivo"
    Crie um divisor de tensão com dois resistores (`R1` e `R2`) para reduzir uma tensão de entrada de `5V` para aproximadamente `3.3V` (essencial para compatibilidade de sinais com o ESP32):

    ```text
    V_out = V_in × [ R2 / (R1 + R2) ]
    ```
    
    1. Escolha valores comerciais adequados para `R1` e `R2` (por exemplo: `R1 = 1.8 kΩ` e `R2 = 3.3 kΩ`, ou `R1 = 1 kΩ` e `R2 = 2 kΩ`).
    2. Meça a tensão de saída `V_out` no multímetro e confirme o valor teórico.

---

## Próximo Passo

Com os conceitos de circuitos, componentes e instrumentação dominados, avance para a **Unidade II** para conhecer a fundo o coração da nossa trilha: a **Família ESP32**, suas variantes modernas e o ecossistema de simulação e virtualização para Linux e Windows.

