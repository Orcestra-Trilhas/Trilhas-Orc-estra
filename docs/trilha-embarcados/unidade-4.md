# Unidade IV – GPIOs, Interrupções, Sensores & Atuadores

---

## 1. Visão Geral

Um microcontrolador ganha utilidade quando interage com o mundo físico através de suas portas de entrada e saída de uso geral (**GPIOs — General Purpose Input/Output**). No ESP32 e na Raspberry Pi Pico 2W, esses pinos são equipados com temporizadores de alta velocidade para modulação por largura de pulso (**PWM**), conversores analógico-digitais (**ADC**) e canais de interrupção de hardware em tempo real (**IRQs**).

Nesta unidade, você aprenderá a acionar saídas digitais, gerar sinais analógicos via PWM, realizar leituras de sensores analógicos e digitais, tratar eventos assíncronos via **interrupções externas** sem travar o processador, e acionar cargas de maior potência (como relés e motores) com segurança.

---

## Módulo 4.1: Entradas, Saídas, PWM & Conversores Analógicos (ADC)

### 1. Entradas e Saídas Digitais

=== "MicroPython (Padrão da Trilha)"
    ```python
    from machine import Pin

    # No ESP32: LED onboard no GPIO 2
    # Na Pico 2W: LED onboard via Pin("LED", Pin.OUT)
    led = Pin(2, Pin.OUT) 

    # Botão de entrada com resistor interno de Pull-Up ativado
    botao = Pin(4, Pin.IN, Pin.PULL_UP)

    if botao.value() == 0:  # Ao pressionar o botão ligado ao GND
        led.value(1)        # Liga o LED
    else:
        led.value(0)        # Desliga o LED
    ```

=== "C/C++ (PlatformIO / Arduino Core)"
    ```cpp
    #include <Arduino.h>

    #define LED_PIN 2
    #define BOTAO_PIN 4

    void setup() {
        pinMode(LED_PIN, OUTPUT);
        pinMode(BOTAO_PIN, INPUT_PULLUP);
    }

    void loop() {
        if (digitalRead(BOTAO_PIN) == LOW) {
            digitalWrite(LED_PIN, HIGH);
        } else {
            digitalWrite(LED_PIN, LOW);
        }
    }
    ```

### 2. Modulação por Largura de Pulso (PWM)
O PWM permite simular saídas analógicas variando a razão cíclica (*duty cycle*) de uma onda quadrada de alta frequência, sendo perfeito para controlar o brilho de LEDs e a velocidade de motores:

Em MicroPython, o método universal `duty_u16()` aceita valores de **16 bits (0 a 65.535)**, padronizando a escala tanto para o ESP32 quanto para a Raspberry Pi Pico 2W:

```python
from machine import Pin, PWM
import time

# Configura PWM no pino do LED a 1000 Hz
pwm_led = PWM(Pin(2), freq=1000)

# Efeito de respiração (fading) suave
for brilho in range(0, 65535, 1000):
    pwm_led.duty_u16(brilho)
    time.sleep_ms(10)
```

### 3. Conversor Analógico-Digital (ADC)
Permite que o microcontrolador leia grandezas contínuas do mundo real (como temperatura de um termistor, luminosidade de um LDR ou a posição de um potenciômetro):

```python
from machine import Pin, ADC

# Configurando o canal analógico:
# No ESP32: ADC nos GPIOs 32 a 39
# Na Pico 2W: ADC nos GPIOs 26, 27 ou 28
potenciometro = ADC(Pin(34)) # ou ADC(Pin(26)) na Pico

# Leitura normalizada em 16 bits (0 a 65535)
valor_bruto = potenciometro.read_u16()

# Conversão para Volts (0V a 3.3V):
tensao = (valor_bruto / 65535) * 3.3
print(f"Leitura: {valor_bruto} | Tensão: {tensao:.2f} V")
```

> [!NOTE]
> **Sensor de Temperatura Interno da Pico 2W**: O processador RP2350 possui um canal ADC interno (ADC 4) conectado a um diodo medidor de temperatura:
> ```python
> sensor_temp = ADC(4)
> tensao = sensor_temp.read_u16() * (3.3 / 65535)
> temperatura = 27 - (tensao - 0.706) / 0.001721
> print(f"Temperatura da CPU: {temperatura:.1f} °C")
> ```

### 4. Sensores de Toque Capacitivo & Botões

- **No ESP32**: Possui canais capacitivos embutidos no silício (`TouchPad`), dispensando botões mecânicos:
    ```python
    from machine import TouchPad, Pin
    touch = TouchPad(Pin(4))
    print(touch.read()) # Retorna valor alto no ar (~600) e baixo ao tocar o dedo (< 200)
    ```
- **Na Raspberry Pi Pico 2W**: Não possui sensores capacitivos no silício do RP2350. Para detecção de toque, utiliza-se botões tácteis mecânicos com pull-up ou módulos sensores capacitivos externos dedicados (como o popular **TTP223**, que entrega sinal digital HIGH/LOW de 3.3V).

---

## Módulo 4.2: Interrupções de Hardware (IRQs)

Em aplicações críticas (como parada de emergência ou contagem de pulsos de encoders), a CPU não pode perder tempo conferindo botões em loops contínuos (*polling*). Configuramos uma **Interrupção de Hardware (IRQ)**: quando o sinal muda no pino, o hardware suspende temporariamente o código principal e executa uma rotina de serviço (*ISR - Interrupt Service Routine*) instantaneamente.

### Implementando IRQ com Debounce em MicroPython:

```python
from machine import Pin
import time

led = Pin(2, Pin.OUT)
botao = Pin(4, Pin.IN, Pin.PULL_UP)

ultimo_tempo = 0

def botao_isr(pin):
    global ultimo_tempo
    tempo_atual = time.ticks_ms()
    # Filtro de repique mecânico (Debounce de 200ms por software)
    if time.ticks_diff(tempo_atual, ultimo_tempo) > 200:
        ultimo_tempo = tempo_atual
        led.value(not led.value()) # Alterna o LED
        print("Interrupção atendida!")

# Configura gatilho na borda de descida (quando o botão é pressionado ao GND)
botao.irq(trigger=Pin.IRQ_FALLING, handler=botao_isr)

# O loop principal continua livre para executar outras tarefas:
while True:
    time.sleep(1)
```

---

## Módulo 4.3: Controle de Cargas de Alta Potência

As portas de 3.3V do ESP32 e da Pico 2W são feitas para tráfego de sinais lógicos (fornecendo apenas ~4mA a ~12mA). Ao acionar cargas indutivas ou de maior corrente:

1. **Transistores MOSFET Canal N (ex: 2N7000 ou IRLZ44N)**: A porta do microcontrolador liga o Gate através de um resistor limitador (100Ω a 330Ω), com um resistor de pull-down de 10kΩ garantindo que o transistor permaneça desligado no boot.
2. **Diodo de Flyback (ex: 1N4007)**: Quando a bobina de um relé ou motor é desenergizada, ela gera um pico indutivo de alta tensão reversa. O diodo em antiparalelo com a carga dissipa esse pico e salva o circuito.

---

## Desafios Práticos da Unidade IV

!!! example "[Exercício 1] Dimmer de LED com Potenciômetro e PWM"
    No simulador Wokwi (ESP32 ou Pi Pico):
    
    1. Conecte um potenciômetro a uma porta ADC e um LED a uma porta com saída PWM.
    2. Leia o valor com `read_u16()` e aplique-o diretamente na razão cíclica do PWM (`duty_u16()`).
    3. Observe a intensidade luminosa do LED variar suavemente de 0% a 100% conforme você gira o botão do potenciômetro.
