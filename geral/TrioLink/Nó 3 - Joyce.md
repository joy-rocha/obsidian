#### Desafio 3 — Consolidação / Interface
Responsável pelo nó de consolidação, com o display TFT 2.4". Este aluno assina os dados agregados do Nó 2 (fusion/bmp_mpu), processa e gera o estado final do sistema, e é dono do framework de exibição/consolidação — a parte visível do sistema como um todo.


# INTERFACE - Biblioteca
**Pygame (A melhor para interfaces 2D personalizadas em Python)**
- **Linguagem:** Python
- **Indicado para:** Criação de telas personalizadas do zero, combinando imagens, formas geométricas e fontes customizadas.
- **Como funciona:** O canvas do Pygame pode ser direcionado diretamente para o dispositivo de display ou executado em modo janela/fullscreen.

**luma.lcd + Pillow (A melhor opção para Python rápido e direto)**
- **Linguagem:** Python
- **Indicado para:** Exibição de texto, indicadores de status, ícones e gráficos simples de linha.
- **Como funciona:** Comunica-se diretamente com o display via barramento SPI (`spidev`). A interface é desenhada utilizando a biblioteca gráfica **Pillow** (PIL) e enviada para a tela.
- **Vantagem:** Extremamente simples de programar, com documentação rica para controladores comuns (ILI9341, ST7789).

https://youtu.be/XAFTSMeujRg?si=f-rBLpDFH9SF1pN8
https://youtu.be/I41wIyXG8Bc?si=Z8xgmacocaHxzvcF


