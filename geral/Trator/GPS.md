Módulo receptor de GP GY-NEO6MV2 (com chip NEO-6M-0-001)

![[Pasted image 20261108184912.png|179]]


- Lib escolhida para o uso do GPS no arduino: **TinyGPS++**

---
## Ligação Básica GPS -> Arduino

*Os módulos GPS se comunicam por texto padrão NMEA através de portas seriais.* 
- **VCC** do GPS → Pino **5V** do Arduino
- **GND** do GPS → Pino **GND** do Arduino
- **TX** do GPS → Pino Digital **RX** (ex: Pino D3 usando _SoftwareSerial_)
- **RX** do GPS → Pino Digital **TX** (ex: Pino D2 usando _SoftwareSerial_)

---
# Usando a lib TinyGPSplus

A biblioteca **TinyGPS++** é utilizada para processar os dados brutos (sentenças NMEA) enviados por módulos GPS (como o popular NEO-6M) e transformá-los em informações fáceis de ler, como coordenadas, velocidade e altitude. 


### Estrutura Básica do Código

Para a biblioteca funcionar, você precisa alimentar o objeto do TinyGPS++ constantemente com os caracteres recebidos pela porta serial do módulo GPS. 

```cpp
#include <TinyGPS++.h>
#include <SoftwareSerial.h>

// Define os pinos de comunicação serial (RX, TX)
SoftwareSerial ss(4, 3); 

// Cria o objeto TinyGPS++
TinyGPSPlus gps;

void setup() {
  Serial.begin(115200);
  ss.begin(9600); // Taxa padrão da maioria dos módulos GPS
}

void loop() {
  // Alimenta o objeto gps com os dados da serial
  while (ss.available() > 0) {
    gps.encode(ss.read());
  }

  // Verifica se a localização foi atualizada e exibe
  if (gps.location.isUpdated()) {
    Serial.print("Latitude: ");
    Serial.println(gps.location.lat(), 6);
  }
}
```

---

## Comandos Básicos (Métodos de Extração)

Após o método `gps.encode()` processar os dados, você pode acessar as propriedades do objeto `gps` usando os seguintes comandos: 

|Dado Desejado|Comando Principal|Métodos de Suporte / Subcampos|
|---|---|---|
|**Localização**|`gps.location`|`.lat()` (Latitude)  <br>`.lng()` (Longitude)  <br>`.isValid()` (Dados válidos)  <br>`.isUpdated()` (Dado novo)|
|**Data (UTC)**|`gps.date`|`.day()` (Dia)  <br>`.month()` (Mês)  <br>`.year()` (Ano)|
|**Hora (UTC)**|`gps.time`|`.hour()` (Hora)  <br>`.minute()` (Minuto)  <br>`.second()` (Segundo)|
|**Velocidade**|`gps.speed`|`.kmph()` (km/h)  <br>`.mph()` (Milhas/h)  <br>`.knots()` (Nós)|
|**Altitude**|`gps.altitude`|`.meters()` (Metros)  <br>`.feet()` (Pés)|
|**Satélites**|`gps.satellites`|`.value()` (Quantidade de satélites conectados)|
|**Precisão**|`gps.hdop`|`.value()` (Diluição horizontal de precisão)|

---

## Exemplo de uso dos comandos

Sempre valide se o sinal do GPS está correto antes de imprimir ou usar a variável através do método `.isValid()` ou `.isUpdated()`:

```cpp
if (gps.location.isValid()) {
  float latitude = gps.location.lat();
  float longitude = gps.location.lng();
}

if (gps.date.isValid() && gps.time.isValid()) {
  int ano = gps.date.year();
  int hora = gps.time.hour(); // Importante: Retorna em horário UTC (GMT 0)
}

if (gps.speed.isValid()) {
  float velocidade_kmh = gps.speed.kmph();
}
```

---

