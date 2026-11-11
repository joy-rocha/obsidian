
---

O projeto consiste no desenvolvimento de um 'Trator' de sensores, onde será monitorado a temperatura, pressão, umidade e localização do mesmo. Neste projeto, também será desenvolvido 3 tipos de implementos (engates) para o trator, sendo identificados e diferenciados via tags RFID (RC522).

Os sensores selecionados para a captura dos dados e monitoramento do status do trator serão um BMP280 para a análise da temperatura e pressão, um  módulo sensor de temperatura e umidade (DHT22) e um módulo Receptor GPS (GY-NEO6MV2) 

---
# Sensor BMP280

É o **componente físico** (o chip). Ele mede temperatura e pressão atmosférica. Para conversar com a placa (Raspberry Pi), ele usa um protocolo de comunicação serial por fios chamado **I2C**.

| ![[Pasted image 20260908154712.png\|130]] | ![[Pasted image 20260908154726.png\|126]] |
| ----------------------------------------- | ----------------------------------------- |

| **SCL / SCK:** Clock Serial (I2C) / Clock (SPI)                                           |
| ----------------------------------------------------------------------------------------- |
| **SDA / SDI:** Dados Seriais (I2C) / Entrada de dados SPI (MOSI)                          |
| **CSB:** Chip Select (mantido em nível ALTO para I2C; mantido em nível BAIXO para SPI)    |
| **SDO:** Saída de dados SPI (MISO) / Seletor de endereço I2C (GND = `0x76`, VCC = `0x77`) |

---
# RFID - RC522

Esse componente é um **Módulo Leitor e Gravador RFID**, modelo **[RFID-RC522](https://www.google.com/search?ibp=oshop&prds=pvt:hg,pvo:29,mid:576462783695185363,imageDocid:14421490267531210180,gpcid:13768523787844020742,headlineOfferDocid:12984525687558684029,catalogid:9753837786186068883,productDocid:14679060500273737649&q=product&sa=X&ved=2ahUKEwjxtdOK7f-XAxXUuJUCHZthH9kQxa4PegYIAAgNEAE)** (baseado no chip **MFRC522** da NXP).Ele possui **8 pinos** na lateral esquerda e utiliza o protocolo de comunicação **SPI** para conversar com o microcontrolador.

![[Pasted image 20261111085514.png|221]]

|Pino na Placa|Nome / Sigla|Função|Onde conectar (Exemplo no Arduino Uno)|
|---|---|---|---|
|**SDA**|Serial Data / SS (Slave Select)|Ativa ou desativa a comunicação com o módulo no barramento SPI.|Pino Digital **10**|
|**SCK**|Serial Clock|Sincroniza a transmissão de dados enviando pulsos de clock.|Pino Digital **13**|
|**MOSI**|Master Out Slave In|Envia dados do microcontrolador (Mestre) para o leitor RFID (Escravo).|Pino Digital **11**|
|**MISO**|Master In Slave Out|Envia os dados lidos do cartão RFID de volta para o microcontrolador.|Pino Digital **12**|
|**IRQ**|Interrupt Request|Avisa o microcontrolador quando um cartão entra no campo de leitura (geralmente não é usado em projetos básicos).|**Não conectar** (deixar livre)|
|**GND**|Ground (Terra)|Referência negativa (0V) da alimentação elétrica.|Pino **GND**|
|**RST**|Reset|Reinicia o módulo ou o coloca em modo de economia de energia.|Pino Digital **9**|
|**3.3V**|Alimentação Positiva|Fornece energia para o circuito funcionar.|Pino **3.3V**|

---
# Módulo DHT22

**Módulo Sensor de Temperatura e Umidade**, modelo **DHT22** (também conhecido comercialmente como **AM2302**). Ele mede a **temperatura** e a **umidade relativa do ar** do ambiente ao mesmo tempo e envia esses valores em formato digital para o microcontrolador.

![[Pasted image 20261111090925.png|252]]

| Pino na Placa | Nome / Sigla         | Função                                                                                  | Onde conectar (Exemplo no Arduino / ESP32)                                   |
| ------------- | -------------------- | --------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------- |
| **DAT**       | Data (Dados)         | Transmite os sinais digitais com os valores de temperatura e umidade lidos pelo sensor. | Qualquer **Pino Digital** (ex: Pino **2** no Arduino ou GPIO **4** no ESP32) |
| **VCC**       | Alimentação Positiva | Fornece energia para o circuito interno do sensor funcionar (aceita de 3,3V a 5V).      | Pino **5V** ou **3.3V**                                                      |
| **GND**       | Ground (Terra)       | Referência negativa (0V) da alimentação elétrica.                                       | Pino **GND**                                                                 |

---
# GY-NEO6MV2

**Módulo Receptor GPS**, modelo **GY-NEO6MV2** (equipado com o chip **u-blox NEO-6M**). Ele capta sinais de satélites GPS em órbita e envia as informações de posicionamento para o microcontrolador por meio de comunicação serial.

![[Pasted image 20261111091352.png|208]]

| Pino na Placa | Nome / Sigla                  | Função                                                                                     | Onde conectar (Exemplo no Arduino / ESP32)                                  |
| ------------- | ----------------------------- | ------------------------------------------------------------------------------------------ | --------------------------------------------------------------------------- |
| **VCC**       | Alimentação Positiva          | Fornece energia para o módulo funcionar (aceita de 3,3V a 5V graças ao regulador interno). | Pino **5V** ou **3.3V**                                                     |
| **RX**        | Receive (Recepção Serial)     | Recebe comandos e configurações enviados pelo microcontrolador.                            | Pino **TX** do microcontrolador (ex: Pino Digital **3** via SoftwareSerial) |
| **TX**        | Transmit (Transmissão Serial) | Envia os dados de localização (protocolo NMEA) do GPS para o microcontrolador.             | Pino **RX** do microcontrolador (ex: Pino Digital **4** via SoftwareSerial) |
| **GND**       | Ground (Terra)                | Referência negativa (0V) da alimentação elétrica.                                          | Pino **GND**                                                                |
_**atenção:** Ligação cruzada (RX e TX), uso em ambiente aberto, e possui bateria de backup.

---
