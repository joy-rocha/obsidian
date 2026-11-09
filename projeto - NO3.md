O projeto consiste em 3 unidades de  Raspberry pi 5 uma conectada a um sensor MPU, uma coctadaa um sensor BMP e outra conectada a um display, a tela deve mostrar os dados coletados pelos sensores e elas vão se comunicar via MQTT. Os dados vão ser recebidos em formato Json como definidos no arquivo de Schema.md.

O que quero pra este projetoé um sistema simples e funcional no padrão ASSERT, sua linguagem deve ser 100% em C. Também deve ter o uso das libs descritas a seguir

***Expectativa de funcionamento do projeto:*** 
- **BspGpio** - lgpiod para a configuração de todos os pinos do projeto
- **BspScreen** -  criação das funções de abstração do c puro para a interface
 - **Display** - usa a BspScreen para gerar o modelo da interface
- **Fusion** - recebe os MQTTs dos outros nós 
- **Main** - integra tudo
- **Makefile** - compila tudo e gera os executáveis


---

| Sinal no display | Pino físico da Pi |
| ---------------- | ----------------: |
| LCD_D0           |                32 |
| LCD_D1           |                33 |
| LCD_D2           |                36 |
| LCD_D3           |                35 |
| LCD_D4           |                38 |
| LCD_D5           |                40 |
| LCD_D6           |                37 |
| LCD_D7           |                13 |

| Sinal   |                    Pino físico |
| ------- | -----------------------------: |
| LCD_RST |                             22 |
| LCD_CS  |                             24 |
| LCD_RS  |                             18 |
| LCD_WR  |                             16 |
| LCD_RD  |                        1 ou 17 |
| GND     | 6, 9, 14, 20, 25, 30, 34 ou 39 |

### Botões Push-Button
- **Botão Navegar (Preto):** BCM 6 $\rightarrow$ **Pino Físico 31**.
- **Botão Enter (Vermelho):** BCM 7 $\rightarrow$ **Pino Físico 26**.
- **Terra (GND) Comum:** **Pino Físico 6** (ou qualquer pino GND da placa).

---
## Lib gráfica GTK
```bash
sudo apt install libgtk-4-dev pkg-config build-essential
```

## Lib para pinagem LGPIOD
```bash
sudo apt update && sudo apt install libgpiod-dev
```

## Lib CJSON
```bash
sudo apt install -y libcjson-dev
```

---
# Display

https://www.lcdwiki.com/2.4inch_Arduino_Display

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

---