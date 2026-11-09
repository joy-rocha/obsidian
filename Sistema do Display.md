- Os módulos `Bsp_*` cuidam do hardware.
- Os módulos `Decode*` interpretam os dados recebidos.
- `Imagem.c/.h` montam o conteúdo visual das telas.
- O `main.c` coordena a inicialização e o ciclo principal: MQTT, decodificação, atualização dos dados, botões e interface.
- O `Makefile` compila e vincula todos os módulos e bibliotecas necessários.

---
``` c
ASSERT/
│
├── Main.c
│
├── Fusion.c
├── Fusion.h
│
├── Imagem.c
├── Imagem.h
│
├── BspDisplay.c
├── BspDisplay.h
│
├── Bsp_Button.c
├── Bsp_Button.h
│
└── Makefile
```

```bash

dentro de assert na rapi5 execute:
sudo apt install -y build-essential liblgpio-dev libmosquitto-dev libcjson-dev
make clean
make
ls
./monitor


caminho do projeto no pc: /home/joycinha/Músicas/ASSERT

```

| Arquivo           | Responsabilidade                                               |
| ----------------- | -------------------------------------------------------------- |
| `Main.c`          | Controla a aplicação, MQTT, navegação e atualização das telas. |
| `Fusion.c/.h`     | Recebe o JSON e extrai os dados dos sensores.                  |
| `Imagem.c/.h`     | Desenha e organiza os elementos visuais das telas.             |
| `BspDisplay.c/.h` | Controla diretamente o display TFT usando GPIO e lgpio.        |
| `Bsp_Button.c/.h` | Controla os botões físicos, leitura e debounce.                |
| `Makefile`        | Compila todos os arquivos e gera o executável.                 |
| `README.txt`      | Instruções de instalação e execução.                           |
**Observação:** a interface recebe o tópico `fusion/final`. O processamento de fusão dos sensores deve ser realizado pelo publicador responsável por esse tópico, não pelo módulo `Fusion.c` da interface. Esse módulo apenas interpreta o JSON recebido.


***DEPENDÊNCIAS***
```bash
sudo apt install -y \
build-essential \
liblgpio-dev \
libmosquitto-dev \
libcjson-dev \
mosquitto-clients
```

---

| Sinal do display | Função                        |
| ---------------- | ----------------------------- |
| D0 a D7          | Barramento paralelo de dados  |
| WR               | Escrita de dados              |
| RD               | Leitura de dados              |
| CS               | Seleção do display            |
| DC               | Seleção entre comando e dados |
| RESET            | Reinicialização               |
| VCC              | Alimentação                   |
| GND              | Terra                         |
| LED/BL           | Iluminação de fundo           |

***Funcionamento do projeto:***
- **BspGpio** - lgpiod para a configuração de todos os pinos do projeto
- **BspScreen** -  criação das funções de abstração do c puro para a interface
 - **Display** - usa a BspScreen para gerar o modelo da interface
- **Fusion** - recebe os MQTTs dos outros nós 
- **Main** - integra tudo
- **Makefile** - compila tudo e gera os executáveis

