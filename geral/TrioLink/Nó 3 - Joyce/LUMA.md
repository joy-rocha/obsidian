Para utilizar a biblioteca `luma.lcd` (e telas compatíveis com a família `luma.oled`), você utiliza o gerenciador de contexto `canvas`, que é baseado na biblioteca de manipulação de imagens **Pillow (PIL)** do Python.

Antes de rodar qualquer script, certifique-se de reativar seu ambiente virtual no terminal:
```bash
source venv/bin/activate
```

### 1. Inicialização Básica e Conexão (I2C)
```python
from luma.core.interface.serial import i2c
from luma.oled.device import ssd1306

# Configura o barramento I2C (no Ubuntu/Raspberry geralmente port=1, address=0x3C)
serial = i2c(port=1, address=0x3C)

# Inicializa o dispositivo com a resolução da tela (ex: 128x64)
device = ssd1306(serial, width=128, height=64)
```

### 2. Comandos Principais de Desenho (`draw`)
```python
from luma.core.render import canvas

with canvas(device) as draw:
    # 1. Escrever Texto: draw.text((x, y), texto, fill="white")
    draw.text((0, 0), "Status: Online", fill="white")

    # 2. Desenhar Linhas: draw.line((x1, y1, x2, y2), fill="white")
    draw.line((0, 15, 128, 15), fill="white")

    # 3. Retângulos / Caixas: draw.rectangle((x1, y1, x2, y2), outline="white", fill="white")
    draw.rectangle((5, 25, 60, 55), outline="white", fill=None) # Apenas contorno

    # 4. Círculos / Elipses: draw.ellipse((x1, y1, x2, y2), outline="white", fill="white")
    draw.ellipse((70, 25, 100, 55), outline="white", fill="white") # Preenchido
```

### 3. Alterando o Tamanho da Fonte
```python
from PIL import ImageFont
from luma.core.render import canvas

# Carrega uma fonte TrueType do sistema com tamanho 20px
# (Se não tiver a fonte TTF, use a padrão com ImageFont.load_default())
try:
    fonte_grande = ImageFont.truetype("DejaVuSans.ttf", 20)
except IOError:
    fonte_grande = ImageFont.load_default()

with canvas(device) as draw:
    draw.text((0, 0), "TEMP:", fill="white")
    draw.text((0, 20), "26.8°C", font=fonte_grande, fill="white")
```

### 4. Limpar e Desligar a Tela

- **Limpar tudo o que está visível:**
```python
device.clear()
```

- **Ajustar contraste / brilho (0 a 255):**
```
device.contrast(128)
```

- **Ocultar / Desligar o visor:**
```python
device.hide() # Desliga os pixels
device.show() # Religam os pixels
```


![[Pasted image 20260904212702.png|201]]


- - -


- **`fill` (Preenchimento):** Define a cor de dentro da caixa.
    
    - `fill="white"` (preenche de branco)
        
    - `fill="#333333"` (preenche de cinza escuro)
        
    - `fill=None` (deixa o fundo transparente)
        
- **`outline` (Borda):** Define a cor da linha de contorno da caixa (opcional).
    
    - `outline="black"` (desenha uma borda preta ao redor)




# Fontes do display

Rode os comandos abaixo no terminal do seu VS Code para baixar duas fontes gratuitas do Google/OpenSource que combinam com o estilo do painel:
```bash
# Fonte Pixel/Retro (para as letras e textos do painel)
wget https://github.com/google/fonts/raw/main/ofl/vt323/VT323-Regular.ttf
```



