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


# DIA 28/09 

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
