
```
{
    "gps": {
      "type": "object",
      "required": ["fix"],
      "properties": {
        "fix": { "type": "boolean" },
        "lat": { "type": ["number", "null"], "minimum": -90, "maximum": 90 },
        "lon": { "type": ["number", "null"], "minimum": -180, "maximum": 180 },
        "alt_m": { "type": ["number", "null"] },
        "speed_kmh": { "type": ["number", "null"] },
        "satellites": { "type": ["integer", "null"], "minimum": 0 }
      }
    },
    "env": {
      "type": "object",
      "properties": {
        "temp_dht_c": { "type": ["number", "null"], "description": "Temperatura DHT (°C)" },
        "humidity_pct": { "type": ["number", "null"], "minimum": 0, "maximum": 100 },
        "temp_bmp_c": { "type": ["number", "null"], "description": "Temperatura BMP280 (°C)" },
        "pressure_hpa": { "type": ["number", "null"], "description": "Pressão atmosférica (hPa)" }
      }
    }
  }
}
```

```
{
  "gps": {
    "fix": true,
    "lat": -7.230712,
    "lon": -35.881124,
    "alt_m": 552.4,
    "speed_kmh": 3.06,
    "satellites": 8
  },
  "env": {
    "temp_dht_c": 27.4,
    "humidity_pct": 62.0,
    "temp_bmp_c": 27.1,
    "pressure_hpa": 951.8
  }
}
```

[ Sensores (JSON) ] ──(Serial)──> [ Fusion ] ──> [ TractorMqtt ] ──> [ Broker MQTT ]
                                   (fusion.c)     (tractor_mqtt.c)

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
