# Gerenciador de memória
- **Alocação e desalocação**: reserva ou libera memória na RAM
- **Controle e mapeamento**: registra em tempo real quais partes da memória estão ocupadas ou livres
- **Proteção e isolamento**: garante que um processo não invada o espaço de memória reservado de outro ou do próprio kernel
- **Gerenciamento de troca**: controla a movimentação de dados entre a RAM e a memória seundária (HS/SSD)

**-> Por que alocar o S.O no começo ou no final da memória interfere no desempenho do sistema**
Porquê se o programa for ler em sequência ele vai ter que percorrer menos ou mais pra acessar e isso varia o tempo e esforço 

---
# Monoprogramação

> É um tipo de sistema utilizado antigamente em que **um único programa é executado por vez na máquina, tendo todos os recursos exclusivos e livres para o processo da vez durante seu uso**

*VANTAGEM*:
- simplicidade
- segurança

*DESVANTAGEM:*
- espera ociosa da CPU
- gargalo da E/S
- lentidão

*OU SEJA:*
Esse sistema é bem simples de ser implementado e não precisa de mecanismos complexos de sincronização ou proteção de memória já que um processo roda por vez. Mas devido a isso a CPU costuma ficar ociosa quando o processo ta usando outro recurso pois ela fica parada, também temos o gargalo da E/S que 'trava' o sistema durante uma leitura já que cada coisa 'feita por vez, gerando lentidão.

---

# Multiprogramação

> Tipo de sistema onde vários processos estão em estado de execução ao mesmo tempo, a ideia central é manter a CPU e os periféricos ocupados todo o tempo por meio de compartilhamento de recursos a fim de gerar a impressão de simultaniedade no sistema.

*VANTAGEM:*
- velocidade 
- alta utilização da CPU
- compartilhamento eficiente de recursos

*DESVANTAGEM:*
- complexidade alta do sistema 
- necessita de proteção de memória
- fragmentação
- problemas de concorrência

---
# Modelo Probabilístico de Multiprogramação

> Esse modelo matemático calcula o **percentual de utilização da CPU** em função da quantidade de programas mantidos na RAM e do tempo que eles passam aguardando operações de Entrada e Saída (E/S).

$$\text{Utilização da CPU} = 1 - p^n$$

- **$p$ (Fração de tempo em E/S):** Probabilidade de um único processo estar bloqueado esperando E/S (ex: $80\% = 0{,}8$).
    
- **$n$ (Grau de Multiprogramação):** Quantidade de processos presentes na memória RAM simultaneamente.
    
- **$p^n$:** A probabilidade de **todos os $n$ processos** estarem esperando por E/S no mesmo instante de tempo (momento em que a CPU fica totalmente ociosa).
    
- **$1 - p^n$:** A probabilidade de **pelo menos um processo** estar pronto para executar, mantendo a CPU ocupada.

---

# Gerenciamento de Memória com :
### Mapas de Bits (_Bitmaps_):
> A memória é dividida em blocos de alocação fixos.

- Cada bloco é representado por **1 bit**: `0` representa um bloco livre e `1` representa um bloco ocupado.

mapa de bits é algo assim:
```
11111100000
11100011111
00011110000
```

*DESVANTAGEM:*
o tamanho do mapa de bits é proporcional ao tamanho da memória alocada

### Tamanho do Mapa de Bits (Overhead / Desperdício)
>"Quanto espaço de memória este Mapa de Bits vai gastar?"

#### A Fórmula
1. **Número de Blocos ($N$):**
$$N = \frac{\text{Tamanho Total da Memória}}{\text{Tamanho da Unidade de Alocação}}$$

2. **Tamanho do Mapa de Bits em Bytes:**
$$\text{Tamanho do Bitmap (bytes)} = \frac{N \text{ (bits)}}{8}$$


### Listas Encadeadas (_Linked Lists_):
>  A memória é representada por uma lista de nós encadeados em ordem de endereço.

```c
[P . 0 . 5] -> [L . 5 . 3]
```
P de processo, que começa no 0 e preenche 5 espaços
L de lacuna, vai do 5 e usar 3 espaços

- Cada nó possui quatro informações: uma flag identificando se é Processo (**P**) ou Lacuna/Buraco (**L** ou **H**), o **endereço inicial**, o **comprimento** (quantidade de blocos) e o **ponteiro** para o próximo nó da lista.

---
# Swapping

> É a **ferramenta** que o gerenciador de memória usa. O Swapping é a ação física de mover processos/dados entre a RAM (memória principal) e o disco (memória secundária/HD/SSD).

- **Swap-Out:** Manda do espaço da RAM para o disco quando a memória enche.
- **Swap-In:** Traz do disco de volta para a RAM quando o programa precisa rodar.

---

# Fragmentação

> _A **fragmentação** é simplesmente **desperdício de memória**. Ela acontece quando existe espaço livre na RAM, mas o Sistema Operacional não consegue aproveitá-lo para carregar e rodar novos programas._

  
### 1. Fragmentação Interna (O desperdício DENTRO da vaga)

 >O SO entrega para o processo um bloco maior do que o tamanho que ele realmente precisa. A sobra do espaço fica presa **dentro** do bloco alocado e não pode ser usada por mais ninguém.
 
- Ocorre em sistemas que usam **tamanhos fixos** de memória (Partições Fixas ou Paginação).

### 2. Fragmentação Externa (O desperdício FORA das vagas)

 > O SO aloca para cada processo o tamanho exato que ele pede. Não há sobra dentro do bloco (zero fragmentação interna). Porém, conforme os programas vão terminando e saindo da RAM em momentos diferentes, eles vão deixando **"buracos" de espaço livre espalhados por toda a memória**.

- Ocorre em sistemas de **tamanho variável** (Partições Variáveis ou Segmentação).

|**Tipo**|**Onde está a sobra?**|**Causa Principal**|**Exemplo Clássico**|
|---|---|---|---|
|**Interna**|**DENTRO** do bloco alocado|Alocação em tamanhos fixos pré-definidos|Processo de $20\text{ KB}$ em vaga de $64\text{ KB}$|
|**Externa**|Espalhada **FORA** dos blocos (buracos)|Alocação variável (processos entram e saem)|$100\text{ MB}$ livres na RAM picotados em vários buracos de $10\text{ MB}$|

***Como o SO resolve cada uma?***

- Para resolver a **Fragmentação Externa**: O SO usa a **Compactação de Memória** (empurra todos os programas para um canto da RAM para juntar os buracos do outro lado) ou passa a usar **Paginação** (alocação não contígua).
    
- Para reduzir a **Fragmentação Interna**: O SO usa tamanhos de partições/páginas menores.


---
