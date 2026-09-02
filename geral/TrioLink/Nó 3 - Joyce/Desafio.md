
---
#### Desafio 3 — Consolidação / Interface
Responsável pelo nó de consolidação, com o display TFT 2.4". Este aluno assina os dados agregados do Nó 2 (fusion/bmp_mpu), processa e gera o estado final do sistema, e é dono do framework de exibição/consolidação — a parte visível do sistema como um todo.

---
# INTERFACE - Biblioteca Luma.lcd

**luma.lcd + Pillow (A melhor opção para Python rápido e direto)**
- **Linguagem:** Python
- **Indicado para:** Exibição de texto, indicadores de status, ícones e gráficos simples de linha.
- **Como funciona:** Comunica-se diretamente com o display via barramento SPI (`spidev`). A interface é desenhada utilizando a biblioteca gráfica **Pillow** (PIL) e enviada para a tela.
- **Vantagem:** Extremamente simples de programar, com documentação rica para controladores comuns (ILI9341, ST7789).

https://youtu.be/XAFTSMeujRg?si=f-rBLpDFH9SF1pN8
https://youtu.be/I41wIyXG8Bc?si=Z8xgmacocaHxzvcF
[instalação]([https://www.piwheels.org/project/luma-lcd/](https://luma-lcd.readthedocs.io/en/latest/software.html#installing-dependencies))

# Instalação da lib
[site_instalação](https://luma-lcd.readthedocs.io/en/latest/software.html)

==Atenção: o uso de ambiente virtual é obrigatório!==

1) Antes de instalar a luma vamos criar um <u>ambiente virtual</u>:
```bash
python3 -m venv ~/luma-env
```

Isso cria um ambiente virtual no diretório inicial chamado `luma-env`e um Python executável em `~/luma-env/bin/python`.

2) Ativação do ambiente virtual:
```bash
source ~/luma-env/bin/activate
```

3) instalação da lib e da biblioteca gráfica Pillow e dependências automaticamente:
   ```bash
   	pip install --upgrade luma.lcd
   ```

*se a instalação (comando 3) falhar, significa que vc precia instalar as dependencias separadamente, então execute o segunte:*
```bash
sudo apt-get update
sudo apt-get install python3-dev libjpeg-dev zlib1g-dev libfreetype6-dev
```

---

# Diplay - Driver IC ILI9341
[Documentação_ILI9341](https://www.lcdwiki.com/2.4inch_Arduino_Display)

| Operating Voltage                        | 5V/3.3V                                                                               |
| ---------------------------------------- | ------------------------------------------------------------------------------------- |
| **Type**                                 | TFT                                                                                   |
| **Resolução Nativa**                     | 320 x 240 pixels                                                                      |
| **Interface de Comunicação**             | SPI                                                                                   |
| **Frequência de Clock SPI (Velocidade)** | 32 MHz a 48 MHz (`bus_speed_hz=32000000` a `48000000`)                                |
| **Orientação (Rotate)**                  | 0 (Retrato), 1 (Paisagem - 90°), 2 (Retrato invertido), 3 (Paisagem invertida - 270°) |
| **Profundidade de Cor**                  | RGB565 (16-bit / 65.536 cores)                                                        |

# Mapeanemto de pinos - GPIO do RPI5

| **Pino do Display ILI9341** | **Função**                 | **Pino na Barra GPIO do Pi 5**           |
| --------------------------- | -------------------------- | ---------------------------------------- |
| **VCC**                     | Alimentação                | Pino 1 (3.3V)                            |
| **GND**                     | Terra                      | Pino 6 (GND)                             |
| **CS (Chip Select)**        | Seleção de dispositivo SPI | Pino 24 (GPIO 8 / SPI0_CE0)              |
| **RESET**                   | Reset do chip              | Pino 22 (GPIO 25)                        |
| **DC / RS**                 | Data / Command             | Pino 18 (GPIO 24)                        |
| **SDI / MOSI**              | Saída de Dados SPI         | Pino 19 (GPIO 10 / SPI0_MOSI)            |
| **SCK / SCLK**              | Clock do SPI               | Pino 23 (GPIO 11 / SPI0_SCLK)            |
| **LED / BL**                | Backlight (Iluminação)     | Pino 1 (3.3V) ou Pino 12 (GPIO 18 / PWM) |

---

# Protocolos de comunicação usados

# MQTT

![[Pasted image 20260902112158.png|363]]

- **Broker (Central de Mensagens):** É o servidor que recebe, filtra e redireciona todas as mensagens. Ele não envia a mensagem diretamente para um dispositivo específico, mas sim para quem se inscreveu no **tópico** correspondente (ex.: `sensores/temperatura`).
    
- **Publisher (Publicador):** Dispositivo que envia (publica) informações para um tópico no Broker. No seu caso, são os sensores remotos (ESP32, Arduino).
    
- **Subscriber (Inscrito / Assinante):** Dispositivo que se registra em um tópico no Broker para receber automaticamente os novos dados sempre que eles chegarem. No seu projeto, é o Raspberry Pi 5.

**O MQTT é um protocolo (uma norma de comunicação), não um programa.** Por isso, ele não vem como um aplicativo pré-instalado único: você precisa escolher e instalar um software que faça o papel de Broker ou utilizar um servidor Broker na nuvem.

# Instalando o Broker - Eclipse Mosquitto

> Ele deve ser instalado no próprio **Raspberry Pi 5**. Ao instalar o Mosquitto na placa, o Pi 5 passa a ser o servidor central da sua rede de sensores.

1. Instalar o servidor e as ferramentas de teste:
```bash 
sudo apt update 
sudo apt install mosquitto mosquitto-clients -y
```

2. Habilitar o serviço para inicializar junto com o sistema:
```bash 
sudo systemctl enable mosquitto
```


## Liberar acesso aos sensores da rede
o Mosquitto vem bloqueado por padrão para conexões externas à placa. Para permitir que sensores enviem dados via Wi-Fi, precisa liberar a porta criando um arquivo de configuração:

1. Abra o editor no terminal:
```bash
sudo nano /etc/mosquitto/conf.d/default.conf
```

2. Cole estas duas linhas dentro do arquivo:
```Plaintext
listener 1883
allow_anonymous true
```

3. Salve o arquivo pressionando `Ctrl + O`, aperte `Enter` e depois saia com `Ctrl + X`.

4. Reinicie o Mosquitto para aplicar as alterações:
```bash
sudo systemctl restart mosquitto
```


## Como testar se funcionou
Abra dois terminais na sua placa:

- **Terminal 1 (Leitor/Subscriber):** Escute um tópico de teste:
```bash
mosquitto_sub -t "teste/topico"
```

- **Terminal 2 (Publicador/Publisher):** Envie uma mensagem:
```bash
mosquitto_pub -t "teste/topico" -m "Mosquitto funcionando!"
```

Se a mensagem aparecer instantaneamente no Terminal 1, o servidor estará pronto para receber os dados dos seus sensores físicos.