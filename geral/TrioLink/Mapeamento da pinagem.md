
Mapeamento completo entre os pinos do ecrã e os pinos físicos do conector GPIO da Raspberry Pi 5:

| **Pino no Display** | **Pino Físico na Raspberry Pi 5**             |
| ------------------- | --------------------------------------------- |
| **LCD_RST**         | **Pino 22** ok                                |
| **LCD_CS**          | **Pino 24** ok                                |
| **LCD_RS**          | **Pino 18** ok                                |
| **LCD_WR**          | **Pino 16** ok                                |
| **LCD_RD**          | **Pino 1 ou 17 (3.3V)** ok                    |
| **GND**             | **Pino 6** (ou qualquer pino GND) ok          |
| **5V**              | **Pino 2 ou 4** ok                            |
| **3V3**             | _Desconectado_ (o shield possui regulador U1) |
| **LCD_D0**          | **Pino 32** ok                                |
| **LCD_D1**          | **Pino 33** ok                                |
| **LCD_D2**          | **Pino 36** ok                                |
| **LCD_D3**          | **Pino 35** ok                                |
| **LCD_D4**          | **Pino 38** ok                                |
| **LCD_D5**          | **Pino 40** ok                                |
| **LCD_D6**          | **Pino 37** ok                                |
| **LCD_D7**          | **Pino 13** ok                                |

![[Pasted image 20260917165742.png|620]]

```bash
scp -r ~/Documentos/TrioLink/cod joyce@rpi5-joyce.local:~/

//garantir as lib nas raps
sudo apt update && sudo apt install -y libcjson-dev liblgpio-dev libfreetype6-dev
```

### Botões Push-Button
- **Botão Navegar (Preto):** BCM 6 $\rightarrow$ **Pino Físico 31**.
- **Botão Enter (Vermelho):** BCM 7 $\rightarrow$ **Pino Físico 26**.
- **Terra (GND) Comum:** **Pino Físico 6** (ou qualquer pino GND da placa).


sudo apt update && sudo apt install libgpiod-dev

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