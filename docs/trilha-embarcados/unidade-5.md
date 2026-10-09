# Unidade V – Protocolos de Comunicação & IoT

---

## 1. Visão Geral

Sensores isolados possuem valor limitado; o verdadeiro potencial da eletrônica moderna se manifesta quando dispositivos trocam dados com periféricos de bancada e se conectam à internet global para alimentar plataformas em nuvem e painéis de controle.

Tanto o **ESP32** quanto a **Raspberry Pi Pico 2W** são equipados com rádios Wi-Fi integrados e controladores de hardware para os principais barramentos da indústria (**UART, I2C e SPI**).

Nesta unidade, você aprenderá a se comunicar com telas gráficas OLED via barramento **I2C**, conectar suas placas a redes **Wi-Fi** usando a API universal `network` do MicroPython e enviar telemetria em tempo real para a nuvem utilizando o protocolo padrão da Internet das Coisas: o **MQTT**.

---

## Módulo 5.1: Barramentos Periféricos (UART, I2C & SPI)

### 1. O Barramento I2C & Displays OLED SSD1306
O **I2C (Inter-Integrated Circuit)** utiliza apenas 2 fios de dados síncronos com resistores de pull-up:
- `SDA` (Serial Data) — Linha bidirecional de dados.
- `SCL` (Serial Clock) — Linha de clock pulsada pelo mestre.

Cada dispositivo no barramento possui um endereço hexadecimal único de 7 bits (por exemplo, a tela gráfica OLED SSD1306 quase sempre responde no endereço `0x3C`).

#### Inicializando I2C em MicroPython:

=== "MicroPython (Universal)"
    ```python
    from machine import Pin, I2C
    import ssd1306

    # Definição dos pinos conforme a placa:
    # No ESP32: SCL no GPIO 22, SDA no GPIO 21
    # Na Pico 2W: SCL no GPIO 9, SDA no GPIO 8 (Barramento I2C0)
    i2c = I2C(0, scl=Pin(22), sda=Pin(21), freq=400000)

    # Escanear barramento para encontrar endereços conectados:
    dispositivos = i2c.scan()
    print("Dispositivos I2C encontrados:", [hex(d) for d in dispositivos])

    # Inicializar tela OLED de 128x64 pixels:
    oled = ssd1306.SSD1306_I2C(128, 64, i2c)
    oled.fill(0) # Limpa a tela
    oled.text("Orc'estra IoT", 10, 10)
    oled.text("Status: ONLINE", 10, 30)
    oled.show()  # Atualiza o buffer na tela física
    ```

=== "C/C++ (PlatformIO / Arduino Core)"
    ```cpp
    #include <Wire.h>
    #include <Adafruit_GFX.h>
    #include <Adafruit_SSD1306.h>

    Adafruit_SSD1306 display(128, 64, &Wire, -1);

    void setup() {
        Wire.begin(); // Pinos padrão da placa
        display.begin(SSD1306_SWITCHCAPVCC, 0x3C);
        display.clearDisplay();
        display.setTextColor(WHITE);
        display.setTextSize(1);
        display.setCursor(10, 20);
        display.println("Orc'estra IoT C++");
        display.display();
    }

    void loop() {}
    ```

### 2. Inspeção de Protocolos: Analisador Lógico Virtual (Wokwi + PulseView)

Em bancadas físicas profissionais, quando um sensor I2C ou SPI não responde, engenheiros utilizam um **Analisador Lógico (*Logic Analyzer*)** conectado às trilhas para visualizar a forma de onda digital e conferir se os bytes estão sendo enviados corretamente.

No simulador **Wokwi**, você pode fazer exatamente a mesma coisa sem gastar nada com equipamentos de bancada:

1. **Adicionando o Analisador Virtual**: No arquivo `diagram.json` do Wokwi, adicione o componente `"wokwi-logic-analyzer"` e ligue seus canais (ex: `D0` e `D1`) às linhas `SCL` e `SDA`.
2. **Captura em Formato VCD**: Ao rodar e pausar a simulação, o Wokwi faz o download automático de um arquivo de captura no formato **`.vcd` (*Value Change Dump*)**.
3. **Decodificação no PulseView (sigrok)**:
   - Abra o arquivo `.vcd` no software livre **PulseView**.
   - Adicione o decodificador de protocolo **I2C** (selecionando qual canal é o SCL e qual é o SDA).
   - O PulseView desenhará visualmente cada bit, pacote de endereço (`0x3C`), condições de START/STOP e bits de confirmação (ACK / NACK)!

**Links:**

- 📖 [Documentação: Wokwi Logic Analyzer](https://docs.wokwi.com/parts/wokwi-logic-analyzer)
- 🌐 [Download Oficial do PulseView / sigrok](https://sigrok.org/wiki/PulseView)

---

## Módulo 5.2: Conectividade Wi-Fi Universal (`network`)

Tanto no ESP32 quanto na Raspberry Pi Pico 2W, a biblioteca oficial `network` do MicroPython disponibiliza a mesma interface padronizada para gerenciamento da interface de rádio (`STA_IF` para conectar a roteadores ou `AP_IF` para criar um ponto de acesso próprio):

### Script Universal de Conexão Wi-Fi:

```python
import network
import time

def conectar_wifi(ssid, senha):
    wlan = network.WLAN(network.STA_IF)
    wlan.active(True)
    
    if not wlan.isconnected():
        print(f"Conectando à rede {ssid}...")
        wlan.connect(ssid, senha)
        
        tentativas = 0
        while not wlan.isconnected() and tentativas < 20:
            time.sleep(0.5)
            tentativas += 1
            print(".", end="")
            
    if wlan.isconnected():
        ip_info = wlan.ifconfig()
        print(f"\nConectado com sucesso! IP obtido: {ip_info[0]}")
        return True
    else:
        print("\nFalha ao conectar no Wi-Fi!")
        return False

# No Wokwi, utilize SSID: "Wokwi-GUEST" e senha: "" (vazia)
conectar_wifi("MinhaRede", "MinhaSenha123")
```

---

## Módulo 5.3: O Protocolo MQTT & Dashboards na Nuvem

O **MQTT (Message Queuing Telemetry Transport)** é o protocolo padrão da indústria de IoT. Diferente do HTTP (onde o cliente precisa fazer requisições ativas para receber respostas), o MQTT é baseado no modelo **Publicador / Assinante (Pub/Sub)** intermediado por um servidor central (**Broker**):

- **Tópicos**: Os dados são organizados hierarquicamente:
  - O microcontrolador publica leituras: `orcestra/dispositivo1/temperatura`
  - O painel em nuvem subscreve no tópico para atualizar gráficos instantaneamente.
  - O painel publica comandos: `orcestra/dispositivo1/rele/set` e o dispositivo recebe o evento em tempo real!

### Exemplo Completo com `umqtt.simple` em MicroPython:

```python
from umqtt.simple import MQTTClient
from machine import Pin
import time

BROKER = "broker.hivemq.com"
CLIENT_ID = "orcestra_dispositivo_01"
TOPICO_TELEMETRIA = b"orcestra/lab/temperatura"
TOPICO_COMANDO = b"orcestra/lab/rele/set"

led = Pin(2, Pin.OUT) # No ESP32 (ou Pin("LED", Pin.OUT) na Pico 2W)

# Callback chamado automaticamente quando o Broker envia um comando:
def mensagem_recebida(topico, mensagem):
    print(f"Comando recebido no tópico {topico}: {mensagem}")
    if mensagem == b"LIGAR":
        led.value(1)
    elif mensagem == b"DESLIGAR":
        led.value(0)

# Inicializar cliente MQTT:
cliente = MQTTClient(CLIENT_ID, BROKER)
cliente.set_callback(mensagem_recebida)
cliente.connect()
cliente.subscribe(TOPICO_COMANDO)
print("Conectado ao Broker MQTT e inscrito nos comandos!")

ultimo_envio = 0

while True:
    # Verifica de forma não-bloqueante se há mensagens recebidas
    cliente.check_msg()
    
    # Publica telemetria a cada 5 segundos:
    if time.ticks_diff(time.ticks_ms(), ultimo_envio) >= 5000:
        ultimo_envio = time.ticks_ms()
        cliente.publish(TOPICO_TELEMETRIA, b"25.4")
        print("Telemetria enviada!")
        
    time.sleep_ms(50)
```

---

## Desafios Práticos da Unidade V

!!! example "[Exercício 1] Estação IoT Virtual no Wokwi"
    No Wokwi (ESP32 ou Raspberry Pi Pico W):
    
    1. Conecte sua placa à rede virtual `Wokwi-GUEST`.
    2. Conecte ao broker público `broker.hivemq.com`.
    3. Abra a ferramenta web gratuita [HiveMQ Web Client](http://www.hivemq.com/demos/websocket-client/) no navegador e veja as mensagens enviadas pela sua placa simulada chegarem em tempo real!
