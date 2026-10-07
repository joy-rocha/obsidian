[ main] ──► Função 1: processar_json()
                             │
                            ▼
                      Função 2: calcular_estado_sistema()
                             │
                            ▼
                      Função 3: renderizar_display()


# Divisão de etapas:

1. **Parser do JSON (Entrada):** Recebe a mensagem do MQTT e converte o payload em variáveis numéricas (`float`/`int`) armazenadas em uma `struct` C utilizando a biblioteca `cJSON`.

2. **Processamento do Estado (Lógica):** Compara as medições numéricas para classificar o sistema em `NORMAL` ou `CRÍTICO`. Se o temporizador estourar por falta de pacotes do Nó 2, essa mesma lógica define o estado como `OFFLINE`.

 3. **Renderização no Display (Saída):** Transmite as informações via barramento SPI para o display TFT 2.4", atualizando os valores numéricos e o rótulo de status na tela em tempo real.

- - -
# Etapa 01

O **Parser** é a ponte entre a rede e a memória do seu programa. Ele transforma um texto genérico recebido da internet em números concretos que o processador consegue usar para fazer cálculos.
  

### 1. O que chega da rede (A Entrada)
Quando o Nó 2 publica uma mensagem no tópico `fusion/bmp_mpu`, a rede Wi-Fi e o protocolo MQTT transmitem apenas uma **sequência contínua de caracteres de texto** (código ASCII/UTF-8).

Para a placa, o que chega é literalmente a string:`"{\"temp\": 25.4, \"press\": 1013.2, \"accel_z\": 9.81}"`

O processador não sabe que `25.4` é a temperatura ou que é um número. Para a linguagem C, isso é apenas um vetor de letras e símbolos sem significado matemático. Você não pode fazer `if (temp > 30)` diretamente com um texto.

### 2. O que a biblioteca de Parser faz por dentro
O processo do parser envolve 3 subetapas fundamentais:
- Validação Sintática (Checagem de Regras)
- Mapeamento de Chave e Valor
- Conversão de Tipo de Dados (Texto para Binário)


### 3. A Saída do Parser (Guarda de Dados)

Após a tradução, a sua função pega esses números convertidos e os grava na sua estrutura de dados (`struct` em C). A saída é um bloco limpo na memória RAM contendo:
  
- `temperatura` = `25.4` (tipo `float`)
- `pressao` = `1013.2` (tipo `float`)
- `aceleracao_z` = `9.81` (tipo `float`)

### 4. Temporizador
	┌──► [Tempo < Limite] ──► Avalia números ──► Estado NORMAL ou CRÍTICO
[ Checagem do Temporizador ] ──┤
    └──► [Tempo > Limite] ──► Ignora números ──► Estado OFFLINE




**Filtro de Kalman:** É um algoritmo matemático usado para corrigir o erro do acelerômetro usando os outros sensores.

---
# INSTLAR O S.O PADRÃO DO RPI5
[(intalar_SO](https://www.raspberrypi.com/software/) 

Ativar lib pra abrir o so
```shell
sudo apt install libfuse2t64
sudo apt install libopengl0
```

Faça toda a configuraçãodo SO

Próximos passos assim que terminar de gravar:
1. Desplugue o pendrive do computador e **coloque em uma das portas USB azuis** (USB 3.0) do Raspberry [Pi 5](https://www.google.com/search?ibp=oshop&prds=pvt:hg,pvo:29,mid:576462898614778390,imageDocid:14153299127839318940,gpcid:5175251518436011891,headlineOfferDocid:17447758671785192383,catalogid:17143439992391461761,productDocid:4884427582560886702,rds:PC_5175251518436011891%7CPROD_PC_5175251518436011891&q=product&sa=X&ved=2ahUKEwiQ28ve_eaWAxWlLrkGHS_5KZAQxa4PegYIAAgVEAI) para o sistema rodar bem mais rápido.
2. Ligue o cabo de energia (USB-C) no [Raspberry Pi](https://www.google.com/search?ibp=oshop&prds=pvt:hg,pvo:29,mid:576462898614778390,imageDocid:14153299127839318940,gpcid:5175251518436011891,headlineOfferDocid:17447758671785192383,catalogid:17143439992391461761,productDocid:4884427582560886702,rds:PC_5175251518436011891%7CPROD_PC_5175251518436011891&q=product&sa=X&ved=2ahUKEwiQ28ve_eaWAxWlLrkGHS_5KZAQxa4PegYIAAgVEAQ).
3. Aguarde cerca de **2 minutos** na primeira inicialização para ele configurar o sistema e conectar no seu Wi-Fi.

Quando passar esse tempo, abra o terminal do seu computador e digite este comando para se conectar a ele:
``` bash
ssh joycinha@raspberrypi.local
```

---

BMP280 (pressão, temperatura, altitude)
MPU6050 (acelerômetro e giroscópio)



# DIA 15/09 - conectar o display com a RPI

Aviso de Segurança Elétrica
Desligue totalmente a fonte de alimentação da Raspberry Pi 5 antes de conectar ou mover qualquer cabo. O contato acidental do pino de 5V com pinos de GPIO de 3.3V pode danificar permanentemente o chip de E/S (RP1) da placa.

# Conexão paraela
## **1.Desligar e posicionar a Raspberry Pi 5:
Remova a fonte de alimentação USB-C e posicione a placa em uma superfície isolante (não metálica), com as portas USB viradas para a direita e a barra preta de 40 pinos no topo.

## **2.Conectar a alimentação e terra:**
Conecte os 3 pinos de energia do conector **J4** do display aos pinos do canto esquerdo da Raspberry Pi:
- **3V3** (Display) ➔ **Pino 1** (3.3V - 1ª coluna, fileira de CIMA)
- **5V** (Display) ➔ **Pino 2** (5V - 1ª coluna, fileira de BAIXO)
- **GND** (Display) ➔ **Pino 6** (GND - 3ª coluna, fileira de BAIXO)

## **3.Conectar as linhas de controle do display:**
Ligue os 5 cabos de sinal do conector **J3** do display aos pinos GPIO correspondentes:
- **LCD_RD** ➔ **Pino 11** (GPIO 17 - 6ª coluna, CIMA)
- **LCD_WR** ➔ **Pino 13** (GPIO 27 - 7ª coluna, CIMA)
- **LCD_RS** ➔ **Pino 18** (GPIO 24 - 9ª coluna, BAIXO)
- **LCD_RST** ➔ **Pino 22** (GPIO 25 - 11ª coluna, BAIXO)
- **LCD_CS** ➔ **Pino 24** (GPIO 8 - 12ª coluna, BAIXO)

## **4.Conectar o barramento paralelo de dados (8 bits):**
Conecte os 8 pinos de dados dos conectores **J1** e **J2** do display:

- **LCD_D0** ➔ **Pino 3** (GPIO 2 - 2ª coluna, CIMA)
- **LCD_D1** ➔ **Pino 5** (GPIO 3 - 3ª coluna, CIMA)
- **LCD_D2** ➔ **Pino 7** (GPIO 4 - 4ª coluna, CIMA)
- **LCD_D3** ➔ **Pino 29** (GPIO 5 - 15ª coluna, CIMA)
- **LCD_D4** ➔ **Pino 31** (GPIO 6 - 16ª coluna, CIMA)
- **LCD_D5** ➔ **Pino 26** (GPIO 7 - 13ª coluna, BAIXO)
- **LCD_D6** ➔ **Pino 21** (GPIO 9 - 11ª coluna, CIMA)
- **LCD_D7** ➔ **Pino 19** (GPIO 10 - 10ª coluna, CIMA)

## **5.Energizar e validar a luz de fundo:**
Reconecte o cabo de alimentação USB-C na Raspberry Pi 5.


- - - 

# DIA 16/09 - tentando instalar um s.o teste no rpi

# Ativação do ambiente virtual
```bash
source ~/luma-env/bin/activate
```


# INTALANDO O S.O LÁ NA LUTAAAA

```bash
sudo apt update && sudo apt install arp-scan -y

sudo dpkg --configure -a

sudo apt install arp-scan -y

sudo arp-scan --localnet | grep -i "Raspberry"

sudo arp-scan --localnet

```

# pra conectar no rpi
```bash
for i in {1..254}; do (timeout 1 bash -c "echo > /dev/tcp/192.168.10.$i/22" 2>/dev/null && echo "SSH Aberto no IP: 192.168.10.$i") & done

ssh joyce@192.168.10.50
![[Pasted image 20260916152804.png]]
sudo apt install arp-scan -y && sudo arp-scan --localnet


```
# IP RPI5
O endereço IP do seu Raspberry Pi 5 é **`192.168.10.50`**


![[Pasted image 20260916152804.png]]
anres era apenas conexão local
- - -

# Personalização do S.O raspibian

![[Pasted image 20260916081802.png|400]]

palavra-passe: triolink

# Passando o so para o rpi5

**Conectar via SSH:** No terminal do seu computador (conectado à rede Wi-Fi `Assert`), execute:
```bash
ssh joyce@rasp.local
```


INCLINAÇÃO



**primeiro abre o vs code, abre a pasta do SD e dps cria um arquivo "ssh", dps salva, fecha  e ejeta, aí pluga na rpi5**

![[Pasted image 20260916154246.png|551]]


**senha: rasp** 

```bash
ssh rasp@raspberrypi.local

ip neighbor

ssh rasp@10.42.0.
```


---

# ATUALIZAR BRANCH EXISTENTE

![[Pasted image 20260916211924.png|426]]

```shell
git checkout nome-da-branch
git add nome-da-pasta/
git commit -m "mensagem"
git push
```



---

# DIA 17/09 - Fazendo os arquivos .C pra ler o json que o NO2 mandar


**COMUNICAÇÃO DO *.c* COM O *.py****
- **Lado do C:** O programa compilado executa a lógica e envia a string JSON para o terminal usando o `printf()`.
    
- **Lado do Python:** O `subprocess.run` executa o binário, intercepta a saída de texto (`capture_output=True`) e a armazena em `resultado.stdout`.
    
- **A Conversão:** O `json.loads()` pega essa string de texto e a transforma em um dicionário ou lista nativa do Python.



#### **Como compilar:** Compile no terminal executando:
``` bash
gcc main.c DecodeBMP.C DecodeMPU.C cJSON.c -lm -o meu_programa
```


# TENTANDO INSTALAR O S.O DE NOVO
![[Pasted image 20260917133456.png|265]]
**senha: 1234**
# **DEU CERTO!**

## USAMOS O CABO
![[Pasted image 20260917172640.png|606]]


# Enviando código do NO3 para a rpi5-joyce

```shell
scp -r ~/Documentos/TrioLink/cod joyce@rpi5-joyce.local:~/
```

![[Pasted image 20260917172944.png]]


# Instalação de Libs e dependências no rpi5-joyce
****Dependências:** python, venv, luma.lcd, C, pillow, cJSON, env*

```shell
//  instala o venv
sudo apt update && sudo apt install -y python3-pip python3-venv

//  Crie uma pasta de ambiente virtual chamada `env` e ative-a
python3 -m venv env
source env/bin/activate

// instala a luma e a evdev
pip install --upgrade luma.lcd evdev

//instala a cJSON
sudo apt install -y libcjson-dev build-essential

//instala o Pygame
pip install pygame

//instala a lgpio
pip install lgpio
```


## Usar o rpi5 pra rodar códigos:
**ativa o ambiente virtual e chama o código**
```bash
source env/bin/activate
python3 teu_codigo.py
```



- - - 

# DIA 18/09 - Tentando rodar o código no display

##### -  *tive que trocar a lib luma pela lgpio, pois a luma não suporta comunicação paralela, apenas spi que não é compatível com o display*


- **Instalar uma biblioteca GPIO compatível:** Adotar uma biblioteca atualizada para o hardware da RPi 5, como a `lgpio` (C/C++) ou a `gpiod` (Python).
    
- **Mapear os pinos no código:** Declarar as variáveis associando-as aos GPIOs exatos da sua tabela (os sinais de controlo CS, RS, WR, RD, RST e os bits de dados D0 a D7).
    
- **Criar as rotinas de comunicação (Bit-banging):** Programar funções básicas para enviar _comandos_ e _dados_, ativando e desativando os pinos na ordem correta para transferir os 8 bits para o display.
    
- **Fazer a inicialização do hardware:** Executar a sequência que ativa o pino de reset e, logo a seguir, enviar a lista de comandos hexadecimais específicos do chip ILI9341 para o ligar, definir o padrão de cores e a orientação da tela.
    
- **Criar as funções de desenho:** Fazer as rotinas finais para definir as coordenadas (X, Y) na tela e enviar as cores correspondentes, permitindo desenhar píxeis individuais, formas geométricas ou carregar imagens.

==(fazer essas instalações dentro do ambiente virtual da rpi)==
# Instalação da lib lgpio
``` BASH
sudo apt install -y liblgpio-dev
```

# biblioteca **numpy** 
**para que o código consiga processar a imagem do Pygame antes de a enviar para o ecrã.**
```shell
pip install numpy lgpio
```
**(isso aqui faz o rpi5 )**

# Acessa as conexões de net do rpi5
```shell
sudo mntui
```



scp -r ~/Documentos/TrioLink/cod joyce@rpi5-joyce.local:~


- - - 

# DIA 21/09 - Tentando rodar o código no display de novo

==o display ligouuuu, deu white screen==

O fluxo funcionará da seguinte maneira:
1. As funções no `Screnns.py` desenham a interface gráfica na memória utilizando o `canvas(device)`.
    
2. Quando o bloco de desenho termina, o `luma` chama automaticamente o método `.display()` do novo `FisicoDevice`.
    
3. O `FisicoDevice` pega nessa imagem, converte a matriz de cores para RGB565 e dispara a função `enviar_para_display_fisico()`, enviando os dados pino a pino via `lgpio` para o controlador ILI9341 do seu ecrã.

# Instalando LIBs
instalando todas as lib de novo pq tava dando uns erros estranhos aaaaa
```bash
pip install luma.core luma.emulator luma.lcd lgpio pygame Pillow
```


---
# DIA 25/09 - trocando a conexão do display pra ver se pega

nova pinagem de conexão
[chat da pinagem](https://chatgpt.com/share/6ab67e1a-ffac-83e9-832c-e31cf1229470)

| MAR2406       | Função            |                GPIO BCM RPi 5 | Pino físico |
| ------------- | ----------------- | ----------------------------: | ----------: |
| **1 LCD_RST** | Reset             |                       GPIO 17 |          11 |
| **2 LCD_CS**  | Chip Select       |                       GPIO 27 |          13 |
| **3 LCD_RS**  | Comando/Dados     |                       GPIO 22 |          15 |
| **4 LCD_WR**  | Write             |                       GPIO 23 |          16 |
| **5 LCD_RD**  | Read              |                       GPIO 24 |          18 |
| **6 GND**     | Terra             |                           GND |          20 |
| **7 5V**      | Alimentação       |                            5V |           2 |
| **8 3V3**     | Alimentação 3,3 V | **não conectar inicialmente** |          -- |
| **9 LCD_D0**  | Data 0            |                        GPIO 5 |          29 |
| **10 LCD_D1** | Data 1            |                        GPIO 6 |          31 |
| **11 LCD_D2** | Data 2            |                       GPIO 12 |          32 |
| **12 LCD_D3** | Data 3            |                       GPIO 13 |          33 |
| **13 LCD_D4** | Data 4            |                       GPIO 16 |          36 |
| **14 LCD_D5** | Data 5            |                       GPIO 19 |          35 |
| **15 LCD_D6** | Data 6            |                       GPIO 20 |          38 |
| **16 LCD_D7** | Data 7            |                       GPIO 21 |          40 |

![[Pasted image 20260925213840.png]]

NADA FUNCIONA MDS DO CÉU

![[Pasted image 20260925214326.png]]

---

# DIA 27/09 - trocando a conexão do display pra ver se pega
https://claude.ai/chat/13fb2f2e-85d5-49c0-b4f0-2a85a57703c3


## Pinagem — Shield → Raspberry Pi 5

| Pino do shield  | Função             | GPIO (BCM)                                   | Pino físico |
| --------------- | ------------------ | -------------------------------------------- | ----------- |
| LCD_RST         | Reset              | GPIO4                                        | 7           |
| LCD_CS          | Chip Select        | GPIO17                                       | 11          |
| LCD_RS          | D/C (comando/dado) | GPIO27                                       | 13          |
| LCD_WR          | Write              | GPIO22                                       | 15          |
| LCD_RD          | Read               | **ligar direto no 3V3** (não usamos leitura) | —           |
| LCD_D0          | Data 0             | GPIO5                                        | 29          |
| LCD_D1          | Data 1             | GPIO6                                        | 31          |
| LCD_D2          | Data 2             | GPIO12                                       | 32          |
| LCD_D3          | Data 3             | GPIO13                                       | 33          |
| LCD_D4          | Data 4             | GPIO16                                       | 36          |
| LCD_D5          | Data 5             | GPIO19                                       | 35          |
| LCD_D6          | Data 6             | GPIO20                                       | 38          |
| LCD_D7          | Data 7             | GPIO21                                       | 40          |
| GND             | Terra              | GND                                          | 6           |
| 5V              | Alimentação        | 5V                                           | 2           |
| 3V3             | —                  | deixar desconectado                          | —           |
| SD_SS/DI/DO/SCK | Cartão SD          | não usados, deixar sem ligar                 | —           |

**Botões físicos:**

| Botão                        | GPIO (BCM) | Pino físico | Outra perna |
| ---------------------------- | ---------- | ----------- | ----------- |
| NAVEGAR (percorre as opções) | GPIO23     | 16          | GND         |
| ENTER (confirma)             | GPIO24     | 18          | GND         |

Um detalhe importante sobre software: na **Raspberry Pi 5** o chip que controla a GPIO mudou (o novo chip **RP1**), então bibliotecas antigas (`RPi.GPIO`, `wiringPi`, `bcm2835`) não funcionam mais direto. Vamos usar a `lgpio`, que é a que o Raspberry Pi OS Bookworm já traz pronta e que oficialmente suporta a Pi 5 (via `/dev/gpiochip4`).

Instale o necessário:

```bash
sudo apt install python3-lgpio python3-pil
pip install luma.core --break-system-packages
```

Agora os arquivos:Agora a aplicação principal com a máquina de estados e leitura dos botões:**O que cada arquivo faz:**

- **`ili9341_parallel.py`** — o driver do display: fala diretamente com o barramento paralelo de 8 bits via GPIO (usando `lgpio`), faz a inicialização do ILI9341 e expõe um objeto `device` compatível com o `canvas()` do luma, então o `Screnns.py` funciona quase sem mudanças.
- **`Screnns.py`** — o seu arquivo original, só com um parâmetro `selecionado` adicionado nas funções de tela que tinham múltiplas opções (`Display_MainScreen`, `Display_ScrennOFF`), para desenhar uma borda azul destacando a opção atual — já que sem touch, você precisa de algum feedback visual de "onde o cursor está".
- **`main.py`** — o loop principal: lê os dois botões com debounce, controla a máquina de estados (ON → MAIN → BPM/MPU/DESLIGAR) e chama as telas do `Screnns.py`.

**Antes de rodar:**

1. Confira `LCD_RD` ligado direto no `3V3` do shield (fisicamente, sem passar por GPIO).
2. Troque `ler_dados_sensores()` em `main.py` pela integração real com a parte de captura (arquivo, socket, fila etc.) — hoje ela só devolve valores fixos para você testar a interface isolada.
3. Se a imagem aparecer espelhada/rotacionada errado, ajuste o valor `rotation` em `ILI9341Parallel()` (tente `0x28`, `0x48` ou `0x88` além do `0xE8` padrão).
4. Rode com `python3 main.py` (não precisa de sudo se seu usuário tiver acesso ao grupo `gpio`).


### O mais importante
Você está usando:
```shell
GPIOCHIP = 4
```

E o seu teste:
```shell
python3 -c "import lgpio; h=lgpio.gpiochip_open(4); print('GPIO CHIP ABERTO:', h); lgpio.gpiochip_close(h)"
```

retornou:
```shell
GPIO CHIP ABERTO: 262145
```

Então **a sua instalação está conseguindo acessar o controlador GPIO**.


![[Pasted image 20260927174213.png|393]]


| Parte             | Resultado              |
| ----------------- | ---------------------- |
| Python/venv       | ✅                      |
| NumPy             | ✅                      |
| `lgpio`           | ✅                      |
| `gpiochip4`       | ✅ alias de `gpiochip0` |
| GPIO RP1          | ✅                      |
| Driver inicializa | ✅                      |
| LCD mostra imagem | ❌                      |

Python
   ↓
lgpio
   ↓
gpiochip4 → RP1
   ↓
GPIO 5
sem erro. ✅

Então, como seu grupo é:
```shell
PINS_DATA = [5, 6, 12, 13, 16, 19, 20, 21]
```

o byte deveria ser enviado assim:
```shell
bit 0 → GPIO 5
bit 1 → GPIO 6
bit 2 → GPIO 12
bit 3 → GPIO 13
bit 4 → GPIO 16
bit 5 → GPIO 19
bit 6 → GPIO 20
bit 7 → GPIO 21
```


---
Perfeito. Isso fecha mais uma etapa: **o GPIO 22 (WR) também está sendo controlado normalmente pelo `lgpio`**.

| Teste               | Resultado |
| ------------------- | --------- |
| `gpiochip4` / RP1   | ✅         |
| GPIO individual     | ✅         |
| Barramento D0–D7    | ✅         |
| `group_write()`     | ✅         |
| WR (GPIO 22)        | ✅         |
| LCD exibindo pixels | ❌         |
# AJUDANTEE - chat
https://chatgpt.com/c/6ab970ea-eb68-83e9-9674-0e1f3bca5531

---


# DIA 28/09 - tentando fazer a interface aparecer no display

### 2. Tabela de Pinagem (Raspberry Pi 5 $\leftrightarrow$ Display Shield)

| **Sinal do Display** | **Pino no Shield** | **GPIO da Raspberry Pi 5** | **Pino Físico na Pi** |
| -------------------- | ------------------ | -------------------------- | --------------------- |
| **VCC**              | 5V                 | 5V Power                   | Pino 2 ou 4           |
| **GND**              | GND                | GND                        | Pino 6 ou 14          |
| **LCD_RST**          | RESET / A4         | **GPIO 25**                | Pino 22               |
| **LCD_CS**           | A3                 | **GPIO 8**                 | Pino 24               |
| **LCD_RS (DC)**      | A2                 | **GPIO 24**                | Pino 18               |
| **LCD_WR**           | A1                 | **GPIO 23**                | Pino 16               |
| **LCD_RD**           | A0                 | **3.3V (fixo)**            | Pino 1 ou 17          |
| **LCD_D0**           | Digital 8          | **GPIO 12**                | Pino 32               |
| **LCD_D1**           | Digital 9          | **GPIO 13**                | Pino 33               |
| **LCD_D2**           | Digital 2          | **GPIO 16**                | Pino 36               |
| **LCD_D3**           | Digital 3          | **GPIO 19**                | Pino 35               |
| **LCD_D4**           | Digital 4          | **GPIO 20**                | Pino 38               |
| **LCD_D5**           | Digital 5          | **GPIO 21**                | Pino 40               |
| **LCD_D6**           | Digital 6          | **GPIO 26**                | Pino 37               |
| **LCD_D7**           | Digital 7          | **GPIO 27**                | Pino 13               |

```bash
sudo apt update
sudo apt install -y build-essential liblgpio-dev libfreetype6-dev pkg-config fonts-dejavu
wget https://raw.githubusercontent.com/DaveGamble/cJSON/master/cJSON.c
wget https://raw.githubusercontent.com/DaveGamble/cJSON/master/cJSON.h
```
# Deu certo !
[ajudanteIA_código](https://gemini.google.com/u/1/app/c5c4c47248475869?utm_source=app_launcher&utm_medium=owned&utm_campaign=base_all)

-> **não tem como usar o touch entt vou usar botões para a seleção** 

| **Botão**    | **Função no Código**     | **Pino Físico na Pi 5** |
| ------------ | ------------------------ | ----------------------- |
| **Preto**    | Navegar (Trocar Seleção) | **Pino 31**             |
| **Vermelho** | Enter (Confirmar)        | **Pino 26**             |
| **Ambos**    | Referência (negativo)    | **Pino 6**              |

---

# DIA 30 /09 - Estudando o novo código e padronizando com as normas do Assert

**LISTA DE TAREFAS:** **✅FEITO***
- animação rapida
- registor de pull up
- tela de ligar
- arrumaer o "desligando"
 

# Visão Geral do Projeto ===========================

O sistema lê dados telemétricos de dois sensores (um barômetro/termômetro **BMP280** e um acelerômetro/giroscópio **MPU6050**), processa essas informações no formato JSON e as exibe em uma interface gráfica customizada no display. A navegação entre as telas (Menu Principal, Tela BMP, Tela MPU e Desligamento) é controlada por botões físicos conectados aos pinos GPIO.

  
## Linguagens de Programação Utilizadas

- **C (C99/C11):** Linguagem principal do projeto. Escolhida pelo alto desempenho, controle direto de memória e acesso de baixo nível aos periféricos de hardware (GPIO e Framebuffer do display).
    
- **GNU Make / Shell Script (Bash):** Utilizados para automação do processo de compilação (`Makefile`) e criação/execução dos scripts no sistema operacional Raspberry Pi OS.


## Bibliotecas Utilizadas

#### Bibliotecas Externas
1. **`lgpio` (`<lgpio.h>`):** Biblioteca moderna de manipulação de GPIOs para a Raspberry Pi (específica para lidar com o novo chip controlador RP1 da Raspberry Pi 5). É usada para ler o estado dos botões físicos com resistores de _pull-up_ internos.
    
2. **`FreeType` (`-lfreetype`):** Biblioteca de renderização de fontes vetoriais. Permite desenhar textos de alta qualidade na tela carregando arquivos TrueType (`DejaVuSansMono-Bold.ttf`).
    
3. **`cJSON` (`cJSON.h` / `cJSON.c`):** Parser leve de JSON em C. Converte strings de dados vindas dos sensores em objetos C manipuláveis.
    
4. **`libm` (`-lm`):** Biblioteca matemática padrão do C para cálculos numéricos.
    
5. **`libpng` (`libpng16`):** Suporte à manipulação e renderização de formatos de imagem na memória gráfica.

#### Bibliotecas Padrão do C
- `<stdio.h>`, `<stdlib.h>`, `<string.h>`: Manipulação de strings, memória e E/S.
    
- `<signal.h>`, `<time.h>`, `<unistd.h>`: Gestão de interrupções de sistema (SIGINT/SIGTERM), contagem de tempo em milissegundos e controle de _sleep/loops_.

### Tipos de Conexão e Comunicação

1. **Entradas Digitais GPIO (Botões Físicos):**
    - **Modo:** Entrada com Pull-Up interno (`LG_SET_PULL_UP`). O pino fica em nível ALTO (1) por padrão e vai para nível BAIXO (0) quando o botão é pressionado contra o GND.
        
    - **Botão Preto (Navegar/Trocar Opção):** Ligado ao **GPIO 12** (Pino Físico 32).
        
    - **Botão Vermelho (Confirmar/Enter):** Ligado ao **GPIO 7** (Pino Físico 26).
        

2. **Interface com o Display TFT (Interface Gráfica):**
    - **Conexão:** Mapeamento de memória via **Framebuffer** (`/dev/fb0` ou similar via driver do display). O código escreve os pixels diretamente no _buffer_ de memória RAM e aplica o envio (`lcd_flush`).

3. **Comunicação com Sensores (BMP280 / MPU6050):**
    - **Protocolo físico real:** Tipicamente bus **I2C** ou **SPI**.
        
    - **Abstração no software:** Os sensores entregam dados em formato string **JSON** contendo temperatura, pressão, altitude (BMP) e aceleração/velocidade (MPU), decodificados pelas funções de parse.


## Arquivos do Projeto e Como se Conectam
A estrutura é organizada de forma modular, separando hardware, lógica de interface, decodificação e controle principal:

```
                  ┌──────────────┐
                  │   main.c     │  (Loop Principal / Eventos)
                  └──────┬───────┘
                         │
        ┌────────────────┼────────────────┬──────────────┐
        ▼                ▼                ▼              ▼
┌──────────────┐ ┌──────────────┐ ┌──────────────┐ ┌───────────┐
│   ui.c/.h    │ │  gfx.c/.h    │ │  lcd.c/.h    │ │Decoders.c │
└──────┬───────┘ └──────┬───────┘ └──────────────┘ └─────┬─────┘
       │                │                                │
       └────────────────┴────────────────────────────────┘
                                │
                         ┌──────▼──────┐
                         │  cJSON.c/.h │
                         └─────────────┘
```

#### Detalhamento de Cada Arquivo

1. **`main.c` (Ponto de Entrada e Loop do Sistema):**
    
    - **Função:** Gerencia o ciclo de vida da aplicação.
        
    - **O que faz:** Inicializa a tela e os GPIOs, faz polling dos botões físicos a cada loop, atualiza os sensores a cada 1 segundo e simula toques para acionar a máquina de estados da UI.
  
2. **`ui.c` / `ui.h` (Interface de Usuário e Máquina de Estados):**
    
    - **Função:** Controla o que aparece na tela e a lógica de navegação.
        
    - **O que faz:** Desenha os cartões do menu principal, as telas detalhadas de cada sensor e a tela de desligamento (`ui_render`). Traduz as coordenadas de clique/botão em ações como mudar de tela ou desligar (`ui_tap`).
  
3. **`gfx.c` / `gfx.h` (Motor Gráfico 2D):**
    
      
    - **Função:** Abstração de desenho vetorial.
        
    - **O que faz:** Desenha retângulos, limpa a tela, converte cores para o formato do display (`rgb()`) e usa a biblioteca _FreeType_ para desenhar textos alinhados na tela.

4. **`lcd.c` / `lcd.h` (Hardware da Tela e Touch):**
    
    - **Função:** Driver de baixo nível do display e da camada de toque.
        
    - **O que faz:** Abre a memória do display (`lcd_init`), envia o buffer desenhado para o painel físico (`lcd_flush`) e faz a leitura bruta/convertida do painel touch (`touch_read`).

5. **`DecodeBMP.c` e `DecodeMPU.c` / `Decoders.h` (Decodificadores de Sensores):**
      
    - **Função:** Camada de dados/telemetria.
          
    - **O que faz:** Recebem as strings JSON brutas dos sensores BMP280 e MPU6050, usam o `cJSON` para extrair os valores numéricos e retornam estruturas C prontas com os dados formatados para exibição.  

6. **`cJSON.c` / `cJSON.h` (Parser JSON):**
    
    - **Função:** Biblioteca utilitária para leitura e manipulação de objetos no formato JSON.

7. **`Makefile` (Script de Compilação):**
      
    - **Função:** Automatiza a compilação de todo o projeto.
        
    - **O que faz:** Chama o compilador `gcc` com todas as flags de otimização (`-O2`), avisos (`-Wall -Wextra`), inclusões de cabeçalhos (`FreeType`, `libpng`) e linka com as bibliotecas do sistema (`-llgpio -lfreetype -lm`).


### Fluxo de Execução Simplificado

1. O `main.c` roda `gfx_init()`, `lcd_init()` e descobre automaticamente o `gpiochip` ativo na Raspberry Pi 5.
    
2. Configura os pinos do **Botão Preto (GPIO 12)** e **Botão Vermelho (GPIO 7)** como entradas _pull-up_.
    
3. Entra em um loop contínuo:
      
    - A cada **1 segundo**, lê os dados JSON dos sensores e decodifica via `Decoders.c`.
        
    - Lê o estado dos **botões físicos**. Se o botão preto for pressionado, avança a opção destacada. Se o vermelho for pressionado, aciona a tela correspondente.
        
    - Se houver alteração de tela ou novos dados, chama `ui_render()` que usa o `gfx.c` para redesenhar a memória e `lcd_flush()` para enviar a imagem final ao display TFT.


---

# DIA 01/10 - arrumando a taxa de atuaizaçõa do display e da troca de telas

[ia_ajudante](https://claude.ai/chat/1ce5e2be-d31e-493d-958c-ca3a51cae047?onboarding=1)

# Como seu projeto funciona

## A ideia geral: camadas

O programa é organizado em camadas, cada uma usando só a de baixo:

```
main.c      → o "chefe": decide quando ler sensores, ler botões e redesenhar
  ui.c      → as telas: o que desenhar e o que cada botão faz
    gfx.c   → o "pincel": desenha retângulos, círculos e texto na memória
      lcd.c → o "carteiro": manda a imagem pronta para o display físico
buttons.c   → lê os dois botões em paralelo (thread)
```

Para desenhar, o programa não fala direto com o display. Ele pinta numa imagem na memória RAM, chamada **framebuffer**, e depois manda essa imagem inteira para o display de uma vez.

## Conceitos básicos de C

- **`.c` e `.h`:** o `.c` tem o código que faz as coisas. O `.h` (header) é só uma lista do que existe naquele módulo, para outros arquivos poderem usar. Quando você escreve `#include "lcd.h"`, está dizendo "quero poder chamar as funções do módulo lcd".
- **Guarda de inclusão (`#ifndef ... #define ... #endif`):** impede que o mesmo header seja lido duas vezes. Foi aí que seu erro de compilação aconteceu: dois headers usavam o mesmo nome de guarda (`LCD_H`), então um foi ignorado.
- **`static`:** "isso é privado deste arquivo". Funções e variáveis `static` não aparecem para os outros.
- **Biblioteca:** código pronto de outra pessoa que você usa. O `Makefile` diz ao compilador quais bibliotecas ligar (`-llgpio`, `-lfreetype`, `-lm`).

## Bibliotecas que você usa

|Biblioteca|Para quê|
|---|---|
|**lgpio**|Ligar e desligar os pinos do Raspberry Pi (GPIO) por código|
|**FreeType**|Ler a fonte `.ttf` e transformar letras em pixels|
|**cJSON**|Ler dados em formato JSON (os dados dos sensores)|
|**pthread**|Rodar duas coisas ao mesmo tempo (a leitura dos botões roda em paralelo)|
|**math (`-lm`)**|Raiz quadrada e funções usadas para desenhar círculos|

## Cada arquivo

### `lcd.h` / `lcd.c`: o driver do display

O display é um **ILI9341** de 320×240 pixels. Ele recebe dados por 8 fios em paralelo (D0 a D7), mais fios de controle:

- **WR:** um pulso que diz "pode ler o que está nos fios de dados agora".
- **DC:** diz se o byte enviado é um comando (0) ou um dado de pixel (1).
- **CS:** diz "estou falando com você" (0) ou "terminei" (1).
- **RST:** reinicia o chip.

Enviar um byte é: colocar os 8 bits nos fios, baixar WR e subir WR de novo.

`lcd_init()` reinicia o chip e manda uma sequência de comandos de configuração (modo paisagem, cores de 16 bits, etc.). Os números do tipo `0xEF, 0x03...` são os valores de calibração recomendados pelo fabricante.

**Cores RGB565:** cada pixel usa 16 bits: 5 de vermelho, 6 de verde e 5 de azul. Por isso cada pixel vira 2 bytes.

`lcd_flush()` manda a imagem para o display, e aqui está tudo o que mudei:

1. **Antes:** a cada byte, o código dava 10 comandos pelo lgpio. Cada comando é lento porque passa pelo sistema operacional. Um quadro tinha cerca de 1,5 milhão de comandos, e isso causava o efeito gradual.
2. **Retângulo sujo:** o driver guarda o quadro anterior (`prev`) e compara com o novo. Só reenvia o retângulo que mudou. Se só a seleção mudou, só aquela região é enviada.
3. **Acesso direto ao hardware (`/dev/gpiomem0`):** o arquivo `/dev/gpiomem0` mostra os registradores do chip de GPIO do Pi 5 como se fossem memória comum. Escrever nessa memória muda os pinos diretamente, sem passar pelo lgpio. Isso é muito mais rápido.
4. **Tabela `lut`:** uma tabela pronta que diz, para cada valor de byte (0 a 255), quais pinos de dados precisam ligar. Assim não há cálculo bit a bit em cada byte.
5. **Verificação de segurança:** antes de usar o modo rápido, o código confere se os pinos já aparecem como saída no registrador. Se não aparecer, desiste e usa o modo lgpio mais lento (fallback).

Também ficam no arquivo funções vazias do **touch** (`touch_init`, etc.), que existem só porque o `lcd.h` as declara. Elas ainda não fazem nada.

### `gfx.h` / `gfx.c`: o pincel

Mantém o framebuffer `fb[320*240]` na memória: cada posição é um pixel de 16 bits.

- **`gfx_fill_rect`:** preenche um retângulo, pixel a pixel.
- **`gfx_fill_rrect`:** retângulo de cantos arredondados. Calcula quanto cada linha precisa "entrar" nos cantos usando raiz quadrada.
- **`gfx_fill_circle`:** círculo. Pinta todo pixel cuja distância ao centro é menor que o raio.
- **`gfx_ring`:** anel, usado nos ícones de liga/desliga e átomo.
- **`put_px`:** põe um pixel, ignorando o que estiver fora da tela, para não escrever fora da memória.
- **`blend_px`:** mistura uma cor com a que já está no pixel, em proporção `a` de 0 a 255. Serve para deixar as bordas das letras suaves (antialiasing).
- **`gfx_text`:** desenha texto. Usa o FreeType para converter cada letra do arquivo de fonte numa pequena imagem em tons de cinza, e depois mistura essa imagem no framebuffer com `blend_px`. A função `utf8_next` entende acentos e caracteres especiais (Ç, Ã, etc.), que em UTF-8 ocupam mais de um byte.
- **`rgb(r, g, b)`:** converte uma cor normal (0 a 255 por canal) para o formato de 16 bits.

### `ui.h` / `ui.c`: as telas

O `ui.h` define os tipos do projeto:

- **`Screen`:** qual tela está aberta (`SCR_HOME`, `SCR_BMP`, `SCR_MPU`, `SCR_OFF`).
- **`UiAction`:** uma ação que o `ui` pede ao `main` (por enquanto, só `ACT_SHUTDOWN`).
- **`SensorData`:** uma "ficha" com os dados de um sensor (online ou não, três valores e o estado).

O `ui.c` faz três coisas:

1. **`ui_render`** desenha a tela atual. Primeiro pinta o fundo, depois usa as funções do `gfx` para colocar cartões, textos, ícones e valores. A função `frame()` desenha o cartão e, se estiver selecionado, uma borda verde.
2. **`ui_next`** é o botão preto: passa para o próximo item da tela (na tela inicial, dá a volta entre os 3 itens).
3. **`ui_enter`** é o botão vermelho: confirma o item. Na tela inicial abre o BMP280, o MPU6050 ou a confirmação de desligar. Nas telas dos sensores, volta. Na confirmação, "SIM" pede o desligamento e "NÃO" volta.

Nada aqui anima nada. As telas trocam porque o `main` chama `ui_render` de novo com outro `Screen`.

### `buttons.h` / `buttons.c`: os botões

Lê os dois botões (GPIO 6 e 7), ligados ao GND com pull-up interno (o pino fica em 1, e vai a 0 quando o botão é apertado).

Uma **thread** (um pedaço do programa rodando em paralelo) verifica os botões a cada 2 ms. Ela faz **debounce**: um botão mecânico "treme" ao ser apertado e gera vários sinais, então o código só aceita a mudança depois de 30 ms estável.

Cada aperto confirmado entra numa **fila**. O `main` chama `btn_poll()` para pegar um evento por vez. Assim nenhum aperto se perde enquanto a tela está sendo redesenhada. O `mutex` (`pthread_mutex_lock`) evita que a thread e o `main` mexam na fila ao mesmo tempo.

### `main.c`: o chefe

Na inicialização, `main()` carrega a fonte (`gfx_init`), liga o display (`lcd_init`) e liga os botões (`btn_init`). Depois entra no laço principal, que se repete até você apertar Ctrl+C:

1. **A cada 1 segundo**, lê os sensores. Hoje são JSONs de teste fixos (`obter_json_bmp` e `obter_json_mpu`), que passam pelos seus decodificadores (`processar_dados_bmp` e `processar_dados_mpu`, nos arquivos `DecodeBMP.c` e `DecodeMPU.c`). Os valores só são guardados, e a tela só é marcada para redesenho, se algo mudou (`memcmp`).
2. **Botões:** pega todos os apertos pendentes e passa para `ui_next` ou `ui_enter`.
3. **Redesenho:** se algo mudou (`dirty = true`), chama `ui_render` para pintar o framebuffer e `lcd_flush` para mandar ao display.
4. Dorme 10 ms e repete, para não gastar CPU à toa.

Outras funções: `desligar_sistema` mostra "DESLIGANDO..." e executa `shutdown`; `--btn-debug` é um modo de teste que só mostra no terminal qual botão foi apertado.

### `Makefile`

É a receita de compilação: lista os arquivos `.c`, as flags (`-O2` otimiza, `-Wall -Wextra` mostram avisos) e as bibliotecas. `make` compila tudo e gera o programa `monitor`; `make clean` apaga o `monitor` para recompilar do zero.

## Arquivos que não vi

`cJSON.c`/`cJSON.h` são a biblioteca de JSON. `DecodeBMP.c`, `DecodeMPU.c` e `Decoders.h` são seus: pegam os dados brutos do sensor e geram um JSON processado com `pressao`, `temperatura`, `estado`, etc. Não os vi, então só sei o que o `main.c` espera deles.

## O caminho completo

```
Botão apertado → thread (debounce) → fila → main → ui_next/ui_enter
→ muda Screen/seleção → ui_render pinta no framebuffer (gfx)
→ lcd_flush manda só o que mudou ao display (lcd)
```

Se quiser, posso mostrar como ler um sensor de verdade no lugar do JSON de teste, ou explicar com mais calma a parte do `lcd.c`, que é a mais difícil.
