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

# Personalização do S.O raspibian

![[Pasted image 20260915152033.png|270]]

palavra-passe: triolink

# Passando o so para o rpi5

**Conectar via SSH:** No terminal do seu computador (conectado à rede Wi-Fi `Assert`), execute:
```bash
ssh joyce@RPIjoy.local
```


INCLINAÇÃO

