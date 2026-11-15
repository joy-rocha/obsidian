
---

O projeto consiste no desenvolvimento de um 'Trator' de sensores, onde será monitorado a temperatura, pressão, umidade e localização do mesmo. Neste projeto, também será desenvolvido 3 tipos de implementos (engates) para o trator, sendo identificados e diferenciados via tags RFID (RC522).

Os sensores selecionados para a captura dos dados e monitoramento do status do trator serão um BMP280 para a análise da temperatura e pressão, um  módulo sensor de temperatura e umidade (DHT22) e um módulo Receptor GPS (GY-NEO6MV2) 

---
# Estrutura do projeto

```
project/
├── inc/
│   ├── Types.h    <-- Structs e tipos de dados compartilhados
│   ├── GpsDriver.h
│   ├── BmpDriver.h
│   ├── Dht22Driver.h
│   ├── RfidDriver.h
│   ├── Fusion.h         <-- Agregador de dados dos sensores
│   ├── MqttService.h    <-- Gerencia conexão e envio MQTT
│   └── Pinout.h  <-- Define a pinagem do sistema
└── src/
    ├── GpsDriver.cpp
    ├── BmpDriver.cpp
    ├── Dht22Driver.cpp
    ├── RfidDriver.cpp
    ├── Fusion.cpp
    ├── MqttService.cpp
    └── Main.cpp       <-- Orquestrador do sistema
```

O sistema vai ser integrado da seguinte forma, teremos um arquivo para definir a pinagem do sistema, um arquivo usado para definir as variáveis usadas no sistema. Também será implementado para cada sensor um arquivo próprio com contato apenas com sua biblioteca específica, um arquivo chamado Fusion será o responsável pela integração dos valores captados em cada polo de sensor, unindo-os e preparando um json com os dados coletados. Na parte de exibição das informações, o arquivo MqttService cuida da parte da conexão e envio MQTT dos dados para um servidor web, enquanto a main é a intermediária entre o Fusion e o MqttService, definindo a frequência com que os sensores são lidos e de quanto em quanto tempo os dados são publicados, monitorar a conexão e ordenar reconexões caso o MQTT caia, sem travar a leitura contínua dos sensores.

---
# Schema json

```json
"gps": {
	"fix": true,
	"lat": -7.230712,
	"lon": -35.881124,
	"alt_m": 552.4,
	"speed_kmh": 3.06,
	"satellites": 8
	}
  
"env": {
	"temp_dht_c": 27.4,
	"humidity_pct": 62.0,
	"temp_bmp_c": 27.1,
	"pressure_hpa": 951.8,
	"engate_rfid": 'reboque'
	}
```

---
# Sensores, protocolos e conexões

| **Módulo / Sensor** | **Protocolo**    | **Pinos no Arduino Mega**                                                                    | **Observação de Ligação**                                                                                                                              |
| ------------------- | ---------------- | -------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **GPS (NEO-6M)**    | UART (Serial)    | **19 (RX1)** e **18 (TX1)**                                                                  | Cruzado: TX do GPS no pino 19; RX do GPS no pino 18.                                                                                                   |
| **BMP180 / BMP280** | I2C              | **20 (SDA)** e **21 (SCL)**                                                                  | Pinos fixos de I2C do Mega.                                                                                                                            |
| **RFID (MFRC522)**  | SPI              | **50 (MISO)**, **51 (MOSI)**, **52 (SCK)**<br><br>  <br><br>+ **53 (SDA/CS)** e **49 (RST)** | Os pinos 50-52 são fixos do barramento SPI. O CS e o RST você escolhe (53 e 49 são o padrão).                                                          |
| **DHT22**           | Digital (1-Wire) | **Pino 2** (ou qualquer digital livre)                                                       | Usa apenas 1 pino de dados comum.                                                                                                                      |
| **Rede (MQTT)**     | SPI ou UART      | _Depende do módulo_                                                                          | Se usar **Ethernet Shield**, ele encaixa direto em cima do Mega (usa SPI + pino 10). Se usar **ESP8266 / ESP-01**, vai na **Serial2 (pinos 16 e 17)**. |

---
# Comunicação - MQTT

O sistema deve contar com o protocolo de comunicação mqtt. Como broker teremos o sistema de dasboard web que vai receber os dados de um aquivo Fusion, responsável pela junção dos valores coletados nos sensores e os transformar num json para serem enviados ao broker, que lá desempacota e expõe dos dados na tela.

**(Fusion.c) ──> (Main_mqtt.c)**
```json
[ Sensores (JSON) ] ──(Serial)──> [ Fusion ] ──> [ TractorMqtt ] ──> [ Broker MQTT ]
```

---
# Dashboard 

A apresentação dos dados coletados nos sensores conectados ao trator serão destinados a uma dashboard num sistema web. O design é bem simples, mostrando o tipo de engate conectado ao trator, o status da conexão, se ta online (recebendo os json direitinho) ou offline (perdeu conexão ou parou de receber json pra atualiza), deve expor os dados de temperatura, pressão, umidade, velocidade,elevação, coordenadas e um mapa de verdade funcional que mostra onde o trator está localizado, igual o sistema do maps que mostra um indicador de onde eu estou, quero algo parecido.

**exemplo base de design para página web**
![[Pasted image 20261115191558.png|353]]

---
# Libs sugeridas

- **MFRC522** - rfid
- **TinyGPSplus** - gps
- **DTHSensorLibrary** - dth
- **Adafruit_BMP280** - bmp

---
# Main e seu fluxo de execução

```txt
loop() infinito:
 ├── 1. Motion_Loop()        -> Roda o tempo todo (controle contínuo dos motores/encoders)
 ├── 2. TractorMqtt_Loop()   -> Roda o tempo todo (mantém conexão e reconecta se cair)
 ├── 3. GpsDriver_Update()   -> Roda o tempo todo (esvazia a Serial do GPS sem perder bytes)
 ├── 4. Rfid_Task()          -> A cada 100 ms (lê tag MFRC522 e envia evento imediato)
 └── 5. Telemetry_Task()     -> A cada 2000 ms (lê DHT + BMP + GPS e envia telemetria)
```

---

## Três Cuidados Práticos no ATmega2560

- **Conflito no barramento SPI:** O módulo Ethernet (W5100/W5500) e o leitor RFID (MFRC522) compartilham os pinos SPI do Mega (`50`, `51`, `52`). Mantenha os pinos `CS` separados (`10` e `53`) e coloque ambos em `HIGH` antes de inicializar qualquer um dos dois, exatamente como está no `setup()`.
    
- **Conversão `dtostrf()` obrigatória:** No compilador AVR do Arduino Mega, `snprintf` com `%.2f` imprime `?` em vez do número para economizar memória flash. Usar `dtostrf()` garante que os floats virem números válidos no JSON.
    
- **Intervalo do DHT (`2000 ms`):** Sensores DHT11/DHT22 exigem cerca de 1,5 s a 2 s entre leituras físicas. Manter `Telemetry_Task()` em `2000 ms` evita leituras falhas (`null`) sem atrasar o movimento do trator.
 
 ---
