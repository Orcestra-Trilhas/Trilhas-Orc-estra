# Unidade III – Programação com MicroPython & Setup de Ambiente

---

## 1. Visão Geral

Desenvolver código para microcontroladores costumava exigir a instalação de compiladores cruzados complexos, longos tempos de compilação e depuração árdua. O **MicroPython** transformou esse cenário ao trazer uma implementação completa da linguagem Python 3 otimizada para rodar diretamente no silício de microcontroladores modernos como o **ESP32** e a **Raspberry Pi Pico 2W**.

Nesta unidade, você configurará o ambiente de desenvolvimento adotado pela Orc'estra: o **Thonny IDE** (para gravação imediata de firmware e uso do REPL interativo) e o **VS Code com a extensão MicroPico** (para versionamento profissional com Git). Além disso, aprenderá a arquitetura de arquivos do MicroPython (`boot.py` e `main.py`), a biblioteca padrão de hardware (`machine`), e verá como esses conceitos se relacionam com o desenvolvimento em **C/C++**.

---

## Módulo 3.1: Setup Profissional (Thonny & VS Code)

### 1. Thonny IDE: Primeiro Contato & Gravação de Firmware

O **Thonny IDE** é o ambiente oficial recomendado pela Raspberry Pi Foundation e amplamente utilizado com o ESP32 por sua simplicidade e ferramentas embutidas.

#### Instalando o Firmware MicroPython:
=== "Raspberry Pi Pico / Pico 2W"
    1. Baixe o arquivo binário mais recente em formato `.uf2` no [site oficial do MicroPython para Raspberry Pi Pico 2](https://micropython.org/download/RPI_PICO2/).
    2. Com a placa desconectada, mantenha pressionado o botão branco **BOOTSEL** na placa e insira o cabo USB no computador.
    3. O computador reconhecerá a placa como um pendrive chamado `RPI-RP2`.
    4. Arraste o arquivo `.uf2` baixado para dentro dessa pasta. A placa reiniciará instantaneamente pronta para rodar MicroPython!

=== "Família ESP32"
    1. Abra o Thonny IDE.
    2. Acesse o menu **Executar (Run) > Configurar Intérprete (Configure Interpreter)**.
    3. Selecione a aba **Intérprete**, escolha **MicroPython (ESP32)** e selecione a porta serial (ex: `COM3` no Windows ou `/dev/ttyUSB0` no Linux).
    4. Clique no link azul no canto inferior direito: **Instalar ou atualizar o MicroPython**.
    5. O instalador detectará a placa e gravará o firmware mais recente via USB em poucos segundos.

#### O REPL Interativo (Read-Eval-Print Loop)
Com a placa conectada, a parte inferior do Thonny exibe o console interativo:
```python
>>> import sys
>>> sys.platform
'rp2' # ou 'esp32'
>>> 2 + 2
4
```
Qualquer linha digitada no REPL é executada imediatamente pela CPU da placa!

### 2. VS Code + Extensão MicroPico (Padrão para Git & Repositórios)

Para projetos estruturados em equipe e versionamento no GitHub:
1. No VS Code, acesse a aba de Extensões (`Ctrl+Shift+X`) e instale a extensão **MicroPico** (mantida por Paul Ober).
2. Conecte a placa via USB. A barra inferior do VS Code exibirá o status de conexão com o dispositivo.
3. Com o comando `MicroPico: Upload project to Pico` na paleta de comandos (`Ctrl+Shift+P`), todos os seus scripts locais são sincronizados com a memória flash da placa.

### 3. A Estrutura de Arquivos no MicroPython

A memória flash da placa passa a funcionar como um pequeno sistema de arquivos:
- **`boot.py`**: É o primeiro arquivo executado no momento da energização. É utilizado para rotinas de inicialização crítica (ex: conectar à rede Wi-Fi da bancada).
- **`main.py`**: Executado logo após o `boot.py`. É onde fica a lógica principal do seu sistema embarcado.

**Links:**

- 🌐 [Download Oficial do Thonny IDE](https://thonny.org/)
- 🌐 [MicroPython Official Downloads](https://micropython.org/download/)
- 🌐 [Extensão MicroPico para VS Code](https://marketplace.visualstudio.com/items?itemName=paulober.pico-w-go)
- 🎥 [Vídeo: Primeiros Passos com MicroPython no Thonny (Fabio Souza)](https://www.youtube.com/watch?v=08N86hk8ZaY)

---

## Módulo 3.2: Fundamentos de MicroPython para Hardware

### 1. O Módulo `machine`
O módulo `machine` é a API universal do MicroPython para controle de periféricos de silício:

```python
import machine

# Configurar GPIO como saída digital
led = machine.Pin(2, machine.Pin.OUT) # No ESP32 (ou machine.Pin("LED", machine.Pin.OUT) na Pico 2W)
led.value(1) # Liga o LED (HIGH)
led.value(0) # Desliga o LED (LOW)

# Configurar GPIO com resistor de pull-up interno para botão
botao = machine.Pin(4, machine.Pin.IN, machine.Pin.PULL_UP)
if botao.value() == 0:
    print("Botão pressionado!")
```

### 2. Temporização Sem Travar o Sistema: `time.ticks_ms()`
Em sistemas embarcados profissionais, nunca utilizamos rotinas bloqueantes longas (como `time.sleep()`), pois elas impedem o microcontrolador de atender sensores e botões. Em MicroPython, a temporização não-bloqueante é feita com:

```python
import time

tempo_anterior = time.ticks_ms()
intervalo = 1000 # 1000 ms = 1 segundo

while True:
    tempo_atual = time.ticks_ms()
    # ticks_diff calcula a diferença lidando corretamente com o overflow do contador de milissegundos
    if time.ticks_diff(tempo_atual, tempo_anterior) >= intervalo:
        tempo_anterior = tempo_atual
        print("1 segundo se passou sem travar a CPU!")
```

### 3. Gerenciamento de Memória & Garbage Collection (`gc`)
Como microcontroladores possuem centenas de kilobytes de RAM (e não gigabytes como um computador), o coletor de lixo do Python entra em ação periodicamente:
```python
import gc

print(f"Memória RAM livre: {gc.mem_free()} bytes")
gc.collect() # Força a liberação de memória não utilizada
```

---

## Módulo 3.3: Alternativa de Baixo Nível: C/C++ com PlatformIO

Para membros que necessitam de máxima performance ou manipulação direta de registradores, a Orc'estra apoia o desenvolvimento em C/C++ utilizando o **PlatformIO** no VS Code:

=== "MicroPython (Padrão da Trilha)"
    ```python
    from machine import Pin
    import time

    led = Pin(2, Pin.OUT)
    while True:
        led.value(not led.value())
        time.sleep(0.5)
    ```

=== "C/C++ (PlatformIO / Arduino Core)"
    ```cpp
    #include <Arduino.h>

    #define LED_PIN 2

    void setup() {
        pinMode(LED_PIN, OUTPUT);
    }

    void loop() {
        digitalWrite(LED_PIN, !digitalRead(LED_PIN));
        delay(500);
    }
    ```

=== "C/C++ (Raspberry Pi Pico SDK Nativo)"
    ```c
    #include "pico/stdlib.h"

    int main() {
        const uint LED_PIN = 25;
        gpio_init(LED_PIN);
        gpio_set_dir(LED_PIN, GPIO_OUT);
        while (true) {
            gpio_put(LED_PIN, 1);
            sleep_ms(500);
            gpio_put(LED_PIN, 0);
            sleep_ms(500);
        }
    }
    ```

---

## Desafios Práticos da Unidade III

!!! example "[Exercício 1] Piscar LED Sem Bloqueio com `ticks_ms` (Bancada Virtual)"
    No simulador Wokwi (ESP32 ou Pi Pico):
    
    1. Crie um script `main.py` que pisca o LED onboard a cada 200ms utilizando `time.ticks_ms()`.
    2. No mesmo loop principal, imprima uma mensagem no console a cada 1000ms, demonstrando que ambas as tarefas ocorrem simultaneamente de forma não-bloqueante.
