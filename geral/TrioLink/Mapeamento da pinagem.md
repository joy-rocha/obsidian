
Mapeamento completo entre os pinos do ecrã e os pinos físicos do conector GPIO da Raspberry Pi 5:

| **Pino no Display** | **Pino Físico na Raspberry Pi 5**             |
| ------------------- | --------------------------------------------- |
| **LCD_RST**         | **Pino 22**                                   |
| **LCD_CS**          | **Pino 24**                                   |
| **LCD_RS**          | **Pino 18**                                   |
| **LCD_WR**          | **Pino 16**                                   |
| **LCD_RD**          | **Pino 1 ou 17 (3.3V)**                       |
| **GND**             | **Pino 6** (ou qualquer pino GND)             |
| **5V**              | **Pino 2 ou 4**                               |
| **3V3**             | _Desconectado_ (o shield possui regulador U1) |
| **LCD_D0**          | **Pino 32**                                   |
| **LCD_D1**          | **Pino 33**                                   |
| **LCD_D2**          | **Pino 36**                                   |
| **LCD_D3**          | **Pino 35**                                   |
| **LCD_D4**          | **Pino 38**                                   |
| **LCD_D5**          | **Pino 40**                                   |
| **LCD_D6**          | **Pino 37**                                   |
| **LCD_D7**          | **Pino 13**                                   |
![[Pasted image 20260917165742.png|551]]


```bash
scp -r ~/Documentos/TrioLink/cod joyce@rpi5-joyce.local:~/

//garantir as lib nas raps
sudo apt update && sudo apt install -y libcjson-dev liblgpio-dev libfreetype6-dev
```


- **Botões Externos** (definidos em `buttons.h` e `buttons.c`):
- **Botão Navegar (Preto)**: Ligar entre o **Pino Físico 31** (BCM 6) e o **GND**.
- **Botão Enter (Vermelho)**: Ligar entre o **Pino Físico 26** (BCM 7) e o **GND**.
