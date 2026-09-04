#### Desafio 1 — Nó BMP280 + BSP / Imagem Yocto

Responsável pelo nó com o sensor BMP280 (pressão, temperatura, altitude). Além de integrar o próprio sensor, este aluno é dono do meta-layer Yocto compartilhado: overlay de device tree para I2C, configuração de kernel, e a convenção de recipe usada para adicionar um sensor à imagem. Os outros dois nós constroem a própria imagem a partir desse layer — ele precisa estar em um repositório git acessível ao grupo desde cedo.




Essa lista descreve como o sistema operacional Linux da sua placa é construído do zero para reconhecer um sensor físico.

  

### 1. Sensor BMP280

É o **componente físico** (o chip). Ele mede temperatura e pressão atmosférica. Para conversar com a placa (Raspberry Pi), ele usa um protocolo de comunicação serial por fios chamado **I2C**.

  

### 2. Meta-layer Yocto Compartilhado

O **Yocto** é uma ferramenta que cria um sistema Linux sob medida para sistemas embarcados (em vez de instalar um Ubuntu pronto, você "compila" seu próprio Linux enxuto).

  

Uma **Meta-layer** (camada) é uma pasta contendo "receitas" de código. Quando ela é **compartilhada**, significa que existe uma camada comum no projeto do grupo que ensina o Yocto a incluir automaticamente os drivers, programas e configurações de sensores para todos os membros da equipe.

  

### 3. Overlay de Device Tree para I2C

Ao contrário de um computador comum (onde você pluga um pendrive e ele é reconhecido na hora), o Linux Embarcado não sabe o que está conectado nos pinos de expansão (GPIO).

  

- **Device Tree (Árvore de Dispositivos):** É o "mapa da mina" que descreve para o Linux todo o hardware da placa.
    
      
    
- **Device Tree Overlay:** É uma modificação/extensão desse mapa. O _Overlay de I2C_ é um pequeno arquivo que avisa o Linux: _"Na linha I2C número 1, no endereço `0x76`, existe um sensor BMP280 conectado"_. Sem isso, o sistema operacional ignora o sensor.
    
      
    

### 4. Configuração de Kernel

O **Kernel** é o coração do sistema operacional, responsável por fazer a ponte entre o software e o hardware.

  

Configurar o Kernel significa ativar as opções internas (os _drivers_) necessárias para que o Linux saiba como traduzir os sinais elétricos do barramento I2C em números de temperatura e pressão. É o ato de "ligar as chaves" de suporte ao I2C e ao BMP280 antes de compilar o sistema.

### Como tudo se conecta na prática:

Plaintext

```
[ Sensor BMP280 ] (Hardware físico)
       │
       ▼ (Conectado via fios I2C)
[ Overlay de Device Tree ] ──► Avisa o Linux em qual pino o sensor está.
       │
       ▼
[ Configuração de Kernel ] ──► Ativa o driver interno para entender o BMP280.
       │
       ▼
[ Meta-layer Yocto ] ────────► Empacota o Kernel + Overlay + Programas em uma imagem Linux pronta.
```












# Aulas - Referências
[yocto_RapsbarryPi](https://youtu.be/fY7u6PiV8qA?si=Uo6zMAoyeyzTpZYh)
[primeiros passos](https://youtube.com/playlist?list=PL7CjOZ3q8fMc7J7aoYrvza52URffS6WRq&si=YpScOeqqVBgblaoR)

#### **ANOTAÇÕES**
DIAGRAMA DE GANTT, PARA USAR NO RELATÓRIO
Diagrama de flechas