
![[Pasted image 20261017164003.png|208]]

---
O **CAN** (_Controller Area Network_) é um protocolo serial veicular e industrial onde um único barramento partilhado interliga todas as unidades de comando. Esse barramento possui os fios `CAN HIGH` e `CAN LOW` que são entrançados para anular interferências eletromagnéticas. Assim, quando um ruído externo afeta ambos os fios na mesma proporção, mantendo a diferença de tensão intacta. Seu funcionamento é baseado na diferença de tenção entre os fios, chamada **Medição Diferencial**. O receptor calcula a tensão através da fórmula:
$$V_{\text{diferencial}} = V_{\text{CAN\_H}} - V_{\text{CAN\_L}}$$

 Com base no valor do cálculo ele:
- **Bit 1 (Estado Recessivo):** Quando ambos os fios ficam na mesma faixa de tensão (diferença próxima de $0\text{V}$), o barramento fica em **estado recessivo** e o circuito lê o bit **1**.

- **Bit 0 (Estado Dominante):** Quando há uma diferença clara de tensão entre os dois fios (aproximadamente $2,5\text{V}$), o barramento entra em **estado dominante** e o circuito lê o bit **0**.

---

## LÓGICA DE COMO USAR O CAN =============================

***==🟡IMPORTAÇÃO e REQUISITOS==***
 Importe o CAN, habilite as interrupçẽos (ele sinaliza quando uma mensagem passa pelo filtro definido) e defina o Baud Rate (Você ajusta a taxa de transmissão da rede) e o software já separa automaticamente o dois pinos para o TX e o RX. 

O STM32 só enxerga dois pinos digitais comuns: **`CAN_TX`** (para transmitir) e **`CAN_RX`** (para receber). Quem pega o sinal do `TX` e transforma fisicamente nas tensões do `CAN_H` e `CAN_L` (e vice-versa, calculando as diferenças de tensão na recepção) é um chip físico externo chamado **Transceiver CAN**.

==***CONFIGURAÇÃO***==
É necessário a configuração de um filtro, que  funciona totalmente via hardware, ele verifica o id da mensagem e se não for do interesse do chip ele já descarta, se for um id válido ele da passagem para o uso da CPU

***==PADRÃO DE MENSAGEM==***
como só tem um barramento para toda a comunicação o CAN possui uma espécie de id que indica o assunto da mensagem (título, categoria, conteúdo da mensagem) 

- **Exemplo:** O ID `0x100` significa _"Velocidade Atual do Veículo"_. O módulo do motor apenas "grita" no barramento: `ID 0x100: 80 km/h`. Quem precisa dessa informação (como o painel e o freio ABS) aceita esse ID no seu filtro. Quem não precisa (como o ar-condicionado) ignora.

==***ENVIO e RECEBIMENTO de mensagens***==
Para o envio de mensagem é necessário preencher a header, nela definimos o id, o tamanho do dados (0 a 8 bytes) e o formato, e os dados são passados num vetor e chama a função de envio.

Já na recepção quando chega uma dado no barramento o callback é chamado e ela já extrai a mensagem pra receber o id e o dado

```
[ Hardware Transceiver ] -> Cuida dos níveis elétricos dos fios CAN_H / CAN_L
        │
[ Pinos TX / RX ]       -> Ligados ao STM32
        │
[ Filtro da HAL ]       -> Deixa passar apenas o ID configurado
        │
[ Interrupção / FIFO ]  -> Dispara o Callback automaticamente
        │
[ Seu Código C ]        -> Lê o vetor de dados e processa a informação
```

---
### ***CAN NA PRÁTICA***

*Sequência rigorosa de configuração no C!*

`HAL_CAN_ConfigFilter()` $\rightarrow$ Configura as regras do filtro primeiro.
`HAL_CAN_Start()` $\rightarrow$ Liga o periférico CAN no chip.
`HAL_CAN_ActivateNotification()` $\rightarrow$ Ativa os avisos por interrupção.

---

No seu código C, **você não lida com tensões nem faz cálculos diferenciais**.

o pino TX manda sinais digitais que são convertidos pelo Transceiver para analógico, gerando as tensões certas no HIGH e no LOW, o RX monitora o CAN_H e o CAN_L, calcula a diferença e transforma em um sinal digital (0 ou 1) enviado pelo RX.


