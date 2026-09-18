https://github.com/joy-rocha/TrioLink
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

1) Instale o pacote do `venv`:
```bash
sudo apt update && sudo apt install python3.14-venv -y
```

2) Antes de instalar a luma vamos criar um <u>ambiente virtual</u>:
```bash
python3 -m venv ~/luma-env
```

Isso cria um ambiente virtual no diretório inicial chamado `luma-env`e um Python executável em `~/luma-env/bin/python`.

3) Ativação do ambiente virtual:
```bash
source ~/luma-env/bin/activate
```

4) instalação da lib e da biblioteca gráfica Pillow e dependências automaticamente:
   ```bash
   	pip install --upgrade luma.lcd
   ```

5) Instale a biblioteca do display e a de MQTT para Python:
```bash
pip install luma.lcd paho-mqtt
```

*se a instalação (comando 4) falhar, significa que vc precia instalar as dependencias separadamente, então execute o segunte:*
```bash
sudo apt-get update
sudo apt-get install python3-dev libjpeg-dev zlib1g-dev libfreetype6-dev
```

- - - 
# para testar só como pc sema rpi5

Rode o comando do gerenciador de pacotes do Linux para baixar as ferramentas de gráficos:
```bash
sudo apt update && sudo apt install -y libsdl2-dev libsdl2-image-dev libsdl2-ttf-dev
```

Instale o emulador no ambiente virtual:
```bash
pip install luma.emulator
```

---
# Intalação da *EVDEV* (lib para capturar o touch do display)

intalação da lib
```bash
pip install evdev
```

Permissão de Leitura (Fundamental no Linux/Raspberry Pi)
```bash
sudo usermod -aG input $USER
```


---
# Diplay - Driver IC ILI9341
[Documentação_ILI9341](https://www.lcdwiki.com/2.4inch_Arduino_Display)

| **Parâmetro**                | **Especificação Correta**                                                             |
| ---------------------------- | ------------------------------------------------------------------------------------- |
| **Driver IC**                | ILI9341                                                                               |
| **Tensão de Operação**       | 5V (Backlight) / 3.3V (Lógica)                                                        |
| **Tipo de Display**          | TFT LCD (2.4")                                                                        |
| **Resolução Nativa**         | 320 x 240 pixels                                                                      |
| **Interface de Comunicação** | **Paralela de 8 bits** (Barramento D0 a D7)                                           |
| **Sinais de Controle**       | LCD_WR, LCD_RD, LCD_RS, LCD_CS, LCD_RST                                               |
| **Orientação (Rotate)**      | 0 (Retrato), 1 (Paisagem - 90°), 2 (Retrato Invertido), 3 (Paisagem Invertida - 270°) |
| **Profundidade de Cor**      | RGB565 (16-bit / 65.536 cores)                                                        |

# Mapeanemto de pinos - GPIO do RPI5

com o scoectores pra baixo :
1\[o o\]2
3\[o o\]4
5\[o o\]6
7\[o o\]8

cima par
baixo impar

![[Pasted image 20260917165842.png|400]]


| **Pino no Display** | **Função / Tipo do Pino**     | **Pino Físico (RPi 5)** | **Nome da GPIO** | **Posição no Conector da RPi 5**  |
| ------------------- | ----------------------------- | ----------------------- | ---------------- | --------------------------------- |
| **3V3**             | Alimentação Lógica (3.3V)     | Pino 1                  | 3.3V             | 1ª Coluna — Fileira de **CIMA**   |
| **5V**              | Alimentação Backlight (5V)    | Pino 2                  | 5V               | 1ª Coluna — Fileira de **BAIXO**  |
| **GND**             | Terra (0V / Ground)           | Pino 6                  | GND              | 3ª Coluna — Fileira de **BAIXO**  |
| **LCD_RD**          | Sinal de Leitura (Read)       | Pino 11                 | GPIO 17          | 6ª Coluna — Fileira de **CIMA**   |
| **LCD_WR**          | Sinal de Escrita (Write)      | Pino 13                 | GPIO 27          | 7ª Coluna — Fileira de **CIMA**   |
| **LCD_RS**          | Seleção Dado / Comando        | Pino 18                 | GPIO 24          | 9ª Coluna — Fileira de **BAIXO**  |
| **LCD_RST**         | Reset do Display              | Pino 22                 | GPIO 25          | 11ª Coluna — Fileira de **BAIXO** |
| **LCD_CS**          | Seleção do Chip (Chip Select) | Pino 24                 | GPIO 8           | 12ª Coluna — Fileira de **BAIXO** |
| **LCD_D0**          | Entrada de Dados (Bit 0)      | Pino 3                  | GPIO 2           | 2ª Coluna — Fileira de **CIMA**   |
| **LCD_D1**          | Entrada de Dados (Bit 1)      | Pino 5                  | GPIO 3           | 3ª Coluna — Fileira de **CIMA**   |
| **LCD_D2**          | Entrada de Dados (Bit 2)      | Pino 7                  | GPIO 4           | 4ª Coluna — Fileira de **CIMA**   |
| **LCD_D3**          | Entrada de Dados (Bit 3)      | Pino 29                 | GPIO 5           | 15ª Coluna — Fileira de **CIMA**  |
| **LCD_D4**          | Entrada de Dados (Bit 4)      | Pino 31                 | GPIO 6           | 16ª Coluna — Fileira de **CIMA**  |
| **LCD_D5**          | Entrada de Dados (Bit 5)      | Pino 26                 | GPIO 7           | 13ª Coluna — Fileira de **BAIXO** |
| **LCD_D6**          | Entrada de Dados (Bit 6)      | Pino 21                 | GPIO 9           | 11ª Coluna — Fileira de **CIMA**  |
| **LCD_D7**          | Entrada de Dados (Bit 7)      | Pino 19                 | GPIO 10          | 10ª Coluna — Fileira de **CIMA**  |
![[Pasted image 20260917165742.png]]
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

5. Se a saída indicar **`active (running)`** em verde, seu broker MQTT está totalmente configurado e pronto para se comunicar com os sensores da rede.
```bash
sudo systemctl status mosquitto
```


---

## Como usar o Mosquitto no terminal (teste)

#### Escutar um tópico (Subscriber)
```bash
mosquitto_sub -h localhost -t "casa/temperatura" -v
```

#### Enviar uma mensagem para o tópico (Publisher)
```bash
mosquitto_pub -h localhost -t "casa/temperatura" -m "24.5"
```

#### Significado dos parâmetros:
- **`-h`** _(host)_: Endereço IP do servidor do Mosquitto (use `localhost` se estiver na mesma máquina, ou o IP do computador se for acessar de um microcontrolador como ESP32/Arduino).
- **`-t`** _(topic)_: O canal de comunicação (ex: `casa/quarto/luz`, `sensores/umidade`).
- **`-m`** _(message)_: O texto ou dado a ser enviado.
- **`-v`** _(verbose)_: Exibe o nome do tópico junto com a mensagem ao receber.

- **`#` (Curinga multinível):** Escuta o tópico atual e todos os seus subtópicos.
	 `mosquitto_sub -h localhost -t "casa/#" -v`
	 (Recebe mensagens de `casa/sala`, `casa/quarto/temperatura`, etc.)_
    
- **`+` (Curinga de nível único):** Substitui apenas uma camada da hierarquia.
    `mosquitto_sub -h localhost -t "casa/+/temperatura" -v`
    (Recebe de `casa/sala/temperatura` e `casa/quarto/temperatura`, mas ignora `casa/sala/luz`)

#### EXEMPLO
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

---

# Intalação da libmosquitto no linux

Biblioteca em C para a utilização do Mosquitto em códigos
```bash
sudo apt install libmosquitto-dev
```

Garantir que tem o compilador instalado:
```bash
  sudo apt install build-essential
```

Incluir no início do seu código C:
```
#include <mosquitto.h>
```

Compilar passando o parâmetro `-lmosquitto` no final:
```bash
gcc programa.c -o programa -lmosquitto
```
(A flag `-lmosquitto` é essencial para dizer ao compilador `gcc` que ele deve linkar a biblioteca do Mosquitto ao criar o executável)


---

# Instalando a  biblioteca para parser de JSON em C - *cJSON*

```bash
sudo apt-get install libcjson-dev
```

No início do arquivo C:
```
#include <cjson/cJSON.h>
```

 Na hora de compilar no terminal:
```bash
gcc programa.c -o programa -lcjson
```

#### **Principais Funções:**

- **`cJSON_Parse(string)`**: Lê a string e cria a estrutura JSON na memória.
    
- **`cJSON_GetObjectItemCaseSensitive(json, "chave")`**: Busca um campo específico pelo nome da chave.
    
- **`cJSON_IsNumber(item)`**: Verifica se o campo encontrado é realmente um número.
    
- **`item->valueint`**: Acessa o valor armazenado como número inteiro (`int`).
    
- **`item->valuedouble`**: Acessa o valor armazenado como decimal (`double` ou `float`).
    
- **`cJSON_Delete(json)`**: Apaga a estrutura da memória após o uso (obrigatório para evitar vazamento de memória).



- - - 
# Desing da case do projeto

[# Ventilador MINI PC](https://pt.aliexpress.com/item/1005009992360134.html?src=google&src=google&albch=shopping&acnt=297-491-5278&isdl=y&slnk=&plac=&mtctp=&albbt=Google_7_shopping&aff_platform=google&aff_short_key=_oFgTQeV&gclsrc=aw.ds&albagn=888888&ds_e_adid=&ds_e_matchtype=&ds_e_device=c&ds_e_network=x&ds_e_product_group_id=&ds_e_product_id=pt1005009992360134&ds_e_product_merchant_id=5657929593&ds_e_product_country=BR&ds_e_product_language=pt&ds_e_product_channel=online&ds_e_product_store_id=&ds_url_v=2&albcp=23461193190&albag=&isSmbAutoCall=false&needSmbHouyi=false&gad_source=1&gad_campaignid=23466249500&gclid=Cj0KCQjw2OnUBhC2ARIsACKyfaG-VB3FjyVGWYRi6VQggB0IEb2Kf8y0v5pMY3sYj0GZeMJolezhnE0aAjPmEALw_wcB)

| ![[Pasted image 20260904093554.png\|305]] | ![[Pasted image 20260904095515.png\|242]] |
| ----------------------------------------- | ----------------------------------------- |

![[Pasted image 20260904095750.png|576]]


---




