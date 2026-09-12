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

