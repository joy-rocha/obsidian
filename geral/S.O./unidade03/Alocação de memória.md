---

---

> É o ato do Sistema Operacional reservar uma região de espaço na memória RAM para armazenar as instruções do programa, suas variáveis e suas estruturas de dados para que a CPU consiga executá-lo.

_Como a CPU só consegue processar dados que já estão na memória principal, o gerenciador de memória precisa encontrar um espaço disponível, marcar essa região como ocupada e garantir que o processo tenha acesso exclusivo a ela._


### 1. Múltiplas Filas (Uma fila por partição)

Cada processo é direcionado para a fila da **menor partição que consiga abrigá-lo** (para tentar evitar o desperdício excessivo).

- **O Grande Problema:** Gera **ociosidade**. A fila de uma partição de 10 KB pode estar cheia de processos esperando, enquanto a partição vizinha de 100 KB está completamente vazia porque não há processos grandes na fila dela. A partição maior fica parada sem fazer nada.

### 2. Fila Única (Todos em uma só fila)

Todos os processos que chegam entram na mesma fila central.

- **Como o SO resolve quem entra:**

    - **Estratégia 1 (Ordem de chegada / FIFO):** Assim que _qualquer_ partição vaga, o SO pega o primeiro processo da fila que caiba nessa partição.
        
    - **Estratégia 2 (Aproveitamento de espaço):** Quando uma partição vaga, o SO varre a fila inteira e escolhe o **maior processo que caiba nela**, reduzindo a fragmentação interna (mas com o risco de deixar processos muito pequenos famintos / _starvation_ no final da fila).


---
# Algoritmos de alocação de memória

## Frist fit
bota o processo na primeira lacuna que encontra
problema: fragmentação
## Best fit
procura a menor lacuna possível que ainda cade o processo
problema: tempo de busca e gera lacunas muito pequenas
## Next fit
aloca uma e a proxima é alocada em realção de onde ele acabou, tipo ele aloca na posição3 e na proxima alocção ele n vai na 1 mesmo se tiver livre ele olha a partir da 3
## Worst fit
procura a maior lacuna possível pra botar o processo 
problema: lento, pois varre a a lista inteira

| **Algoritmo** | **Onde aloca?**                       | **Tempo de busca** | **Principal problema**                                         |
| ------------- | ------------------------------------- | ------------------ | -------------------------------------------------------------- |
| **First Fit** | 1ª lacuna que couber                  | Rápido             | Acumula pequenos fragmentos no início da lista                 |
| **Next Fit**  | 1ª lacuna a partir da última alocação | Rápido             | Espalha e quebra blocos grandes do final da memória            |
| **Best Fit**  | Menor lacuna que couber               | Lento (varre tudo) | Deixa "microburacos" inúteis (fragmentação externa)            |
| **Worst Fit** | Maior lacuna disponível               | Lento (varre tudo) | Acaba com os blocos grandes necessários para programas maiores |

## Listas separadas e Quick fit

> Em vez de ter uma única lista encadeada para a memória inteira, o SO mantém **várias listas encadeadas separadas**, cada uma dedicada a um tamanho comum de lacuna (ex.: uma lista só de buracos de 4 KB, outra para 8 KB, outra para 16 KB, e assim por diante).

- lista separadas para tamanhos diferentes
- encontrar uma lacuna do tamanho solicitado é rápido
- pode haver fragmentação e desperdício de memória
- ganha tempo alocando e perde tempo e memória no desalocar

_Quando um programa é encerrado e libera memória, o SO precisa checar se o bloco vizinho na RAM física também está livre para fundi-los em um buraco maior. Se dois blocos de 4 KB vizinhos forem fundidos, eles viram um bloco de 8 KB, o que exige retirar nós de uma lista e reinseri-los em outra. Essa manutenção de fusão das listas é custosa e lenta. E se a lista do tamanho exato solicitado estiver vazia, o SO precisa buscar em uma lista de blocos maiores e fatiá-lo, o que volta a gerar o problema de fragmentação e sobras.

***ENTÃO TEMOS 5 ALGORITMOS DIFERENTES DE ALOCAÇÃO***

---

# Relocação de Memória

> É o processo de mapear os endereços das instruções e variáveis do programa para os endereços físicos onde ele realmente foi armazenado na RAM.

#### O Problema
Quando um programa é compilado, o compilador gera o código assumindo que a memória começa no **endereço $0$** (endereços lógicos/virtuais). Porém, em sistemas multiprogramados com alocação contígua, o programa pode ser carregado em qualquer posição física da RAM (por exemplo, a partir do byte $100.000$).

## Tipos de Relocação

###  **Relocação Estática (no Carregamento / Software):**

> Realizada pelo **Carregador** (_Loader_) do Sistema Operacional no momento em que o programa é gravado na RAM. O _loader_ modifica diretamente as instruções do arquivo binário, somando o endereço inicial da partição a todas as posições de memória citadas no código.

 - **Desvantagem:** Carregamento lento. Uma vez alocado, o processo **não pode ser movido** de lugar na RAM durante sua execução (dificulta o _swapping_ e a compactação).

  
### **Relocação Dinâmica (na Execução / Hardware - MMU):**

> Realizada em tempo real a cada instrução executada, com suporte do chip de hardware **MMU**. O código do programa permanece intacto (com endereços começando em $0$). A MMU utiliza dois registradores especiais:
   
- **Registrador de Base (ou de Relocação):** Armazena o endereço físico de início da partição na RAM.
- **Registrador de Limite:** Armazena o tamanho total da partição reservada para o processo.

#### Como a MMU Processa a Relocação Dinâmica
```
   Endereço Lógico (CPU)
            │
            ▼
   ┌────────────────────┐      Sim (>=)    ┌──────────────────────────┐
   │  Lógico >= Limite? ├─────────────────►│ ERRO: Violação de Acesso │
   └────────┬───────────┘                  │    (Trap / Segfault)     │
            │ Não (<)                      └──────────────────────────┘
            ▼
   ┌──────────────────┐
   │  Lógico + Base   │ ───► Endereço Físico na RAM
   └──────────────────┘
```

1. A CPU gera um **Endereço Lógico** (ex: posição $50$).

2. A MMU testa se o endereço é válido: $\text{Endereço Lógico} < \text{Registrador de Limite}$.

-  Se for maior ou igual, a MMU interrompe a execução e gera uma falha de proteção de memória (_segmentation fault_).

3. Se for válido, a MMU calcula o endereço físico real:

$$\text{Endereço Físico} = \text{Endereço Lógico} + \text{Registrador de Base}$$

4. **Vantagem:** Permite mover o programa para qualquer outro local da RAM a qualquer instante (basta o SO atualizar o valor contido no **Registrador de Base**)

5. **Desvantagem**: Vai precisar de uma soma e um 'IF' a cada acesso de memória. (OCIOSO) -> ==🔴CONFERIR COM O PROF ISSO AQUI==

---
