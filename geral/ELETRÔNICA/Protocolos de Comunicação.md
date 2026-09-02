# UART 
Universal Asynchronous Receiver Transmitter - Transmissor e Receptor Assíncrono Universal

utiliza apenas dois fios um envia os dados e ooutro recebe osdados, a palavra assíncronno  significa que NÃO há clk compartilhado entre os dois dispositivos. Em vez disso, ambas as partes concordam com uma velocidade de comunicaçãoantesde começarem a conversar, esta velocidade é chamada de boud rate (taxa de transmissão) . as taxas mais comuns são 9600 e 115200 bits por segundo

![[Pasted image 20260829183221.png|295]]


- **TX (Transmit):** Pino de transmissão de dados.
- **RX (Receive):** Pino de recepção de dados.
- **GND (Ground):** O terra de referência comum que deve ser interligado. 

_Nota: O pino **TX** do dispositivo A conecta-se obrigatoriamente ao pino **RX** do dispositivo B e vice-versa_

point to point - só pode conectar dois dispositivos por vez
não possui coreçãode erros integrada, ou seja é necessário a adção de verificações próprias para identificar quando dados forem transmitidos

# MQTT
É um protocolo de *comunicaçãode de rede* que utiliza um modelo de publicação e assinatura, criado especialmente para a Internet das Coisas (IoT)

Apesar do MQTT não precisar de conexões físicas ele não conecta diretamente um sispositivo a outro, ele usa um intermediador chamado **broker**

![[Pasted image 20260902111451.png|323]]

**Broker**: A cental de mensagens. Ele é responsável poraorganizar e enviar a mensagem para seu destinatário.
**Publisher** (publicador): São os sensores que enviam valores para o broker.
**Subscriber** (leitor): Avisa ao broker que quer ler determinado sensor.

**O MQTT é um protocolo (uma norma de comunicação), não um programa.** Por isso, ele não vem como um aplicativo pré-instalado único: você precisa escolher e instalar um software que faça o papel de Broker ou utilizar um servidor Broker na nuvem.

TCP/IP
# SPI
Serial Periphenal Interface - Interface Periférica Serial

protocloco de comunicação física
utiliza 4 fios
um de clck, que mantém os dispositivos sinncronizados
um MOSI (Master Out Salve In - mestre de saída, escravo de entrada) que envia os dados mestre para o escravo (ou COPI)
um MISO (Master in Salve Out - mestre de entrada e escravo de saída ) o escravo usa para enviar os dados de volta para o mestre (ou CIPO)
O **CS/SS (Chip Select / Slave Select)** serve para o Mestre escolher **com qual dispositivo ele quer conversar**
bem rápido e pode chegar a dezenas de megabits por segundo
cada dispositivo conecado precida do seu próprio fio de chip selector



![[Pasted image 20260829192210.png|293]]

# I2C
Inter Integrate Circuit - Circuito Inter-Intergrado

uiliza apenas dois fios e pode conectar até 127 dispositivos
SDA para dados
SCL para o clk

cada barramento no I2C possui um endereçoúnico
é lento pq todos os dispositivos usam os mesmos 2 fios, entt pode ter engarrafamento
como funciona:
o I2C pega o endereço no qual representa o dispositivo que ele quer se comunicar e envia nos barramentos, todos os dispositivos escutam a chamada, mas sóo correspondente responte

# CAN
Controller Area Network - rede de área de conrolador

projetado para carros
usa dois fios
CAN High
CAN Low

posssui um sistema de prioridade integrado chamado arbitration

# RS-232
conecta dois dispositivos numa conexão point topoint simples

os dados são transmitidos por niveis de tensão para reprensetar 0 e 1
o zero (nivél baixo) é representado por uma tensão POSITIVA +15V
o um (nível alto) é representado por uma tensão NEGATIVA -15V


https://youtu.be/2LaiScfoYGQ?si=RXuyfi7Q0a1e18k8