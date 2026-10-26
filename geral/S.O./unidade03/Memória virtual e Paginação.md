_A memória virtual e a paginação andam lado a lado porque a **paginação é o mecanismo de hardware e software que viabiliza a memória virtual na prática**_

---
# Memória Virtual

> Quando a quantidade total de programas excede a memória RAM física disponível (por exemplo, tentar executar um programa de 16 MB em um sistema com apenas 4 MB de RAM), a memória virtual permite **dividir o programa em pedaços e manter na RAM apenas as partes que estão sendo usadas a cada momento**. O restante permanece no disco e é trazido para a memória principal apenas quando necessário.

- **Isolamento e Ilusão de Memória:** Oferece ao processo a ilusão de possuir um espaço de memória contínuo e exclusivo para si (espaço de endereçamento virtual), garantindo proteção e impedindo que um programa acesse a memória de outros processos ou do Kernel.

---
# Paginação

> Técnica de alocação não contígua que divide o espaço de endereçamento do processo em blocos de tamanho fixo chamados **Páginas** (páginas lógicas), e a memória física (RAM) em blocos de mesmo tamanho chamados **Quadros de Página** ou **Molduras** (_Page Frames_). Isso permite alocar qualquer página em qualquer quadro livre na RAM, eliminando a fragmentação externa.

*OU SEJA*:
_paginação é particionar o processo em partes bem pequenas chamadas de páginas para conseguir alocar nos espacinhos_ físicos da memória chamados molduras

- **Alocação Não Contígua:** o processo pode ser dividido em partes menores e ser armazenado de forma separada
- **Fim da Fragmentação Externa:** Como todas as páginas e molduras possuem exatamente o mesmo tamanho , qualquer página cabe em qualquer moldura livre na RAM, eliminando o problema dos buracos inutilizados entre blocos
-  Endereços virtuais estão mapeados na memória física 
	  [espaço virtual] -> [memória física]

-  Páginas e molduras tem o mesmo tamanho sempre em potências de 2
-  Transferências de disco são sempre em unidade de página

---

# Endereço virtual

$$\text{Endereço Virtual} = [\ \text{Número da Página Virtual } (p) \ \mid \ \text{Deslocamento / Offset } (d) \ ]$$

é dividido internamente em duas partes:
1. **Número da Página Virtual:** Indica a "página" em que a informação está.
2. **Deslocamento (****Offset****):** Indica a posição exata do byte dentro dessa página.

***ATENÇÃO:*** 
> O **deslocamento NUNCA muda** durante a tradução! Ele é apenas copiado diretamente do endereço virtual para o endereço físico. Apenas a página virtual é substituída pela moldura física correspondente.


### Passo a Passo do Cálculo da Tradução

$f$ -> frame / moldura

Na tabela de páginas temos o bit **Presente/Ausente (P/A)**:
- Se **P/A = 1**, a página está na RAM física em uma moldura 
- Se **P/A = 0**, a página está no disco e a MMU gera uma interrupção de **Falta de Página (****Page Fault****)

3. **Montagem do Endereço Físico:**
-  A MMU pega a moldura física e junta com o deslocamento mantido.


```
              Endereço Virtual
            ┌─────────┬──────────┐
            │    p    │    d     │
            └────┬────┴─────┬────┘
                 │          │
                 ▼          │
        ┌─────────────────┐ │
        │Tabela de Páginas│ │
        ├─────────────────┤ │
        │ p │ P/A │Moldura│ │
        ├───┼─────┼───────┤ │
        │...│ ... │  ...  │ │
        │ p │  1  │   f   │ │ ──┐ (Se P/A = 0 ──► Page Fault!)
        └───┴─────┴───────┘     │
                                ▼
	                    Endereço Físico
	                  ┌─────────┬─────────┐
	                  │    f    │    d    │
	                  └─────────┴─────────┘
```

1. **Separar $p$ e $d$:** A MMU identifica o número da página ($p$) e o deslocamento ($d$).
    
2. **Consultar o Bit P/A (Presente/Ausente):**
      
    - Se **`0`**: Ocorre um **Page Fault** (o SO precisa buscar a página no disco).
    - Se **`1`**: A página está na RAM. A MMU lê o número da **Moldura Física ($f$)**.
          
3. **Montar o Endereço Físico:** A MMU substitui $p$ por $f$ e mantém o mesmo deslocamento $d$.

---
### Método 1: Cálculo em Decimal (Regra de Três / Divisão)

#### Exemplo de Prova

> **Dados:**
> - Tamanho da página: $4\text{ KB}$ ($4096\text{ bytes}$).
> - Endereço Virtual solicitado: $10000$.
> - A Tabela de Páginas indica que a Página $2$ está carregada na **Moldura 5** (Bit P/A = 1).

> **Pergunta:** Qual é o Endereço Físico correspondente na RAM?


**Passo 1: Descobrir $p$ e $d$ no endereço virtual**  

- **Número da Página ($p$):** Divisão inteira do endereço virtual pelo tamanho da página.

$$p = \lfloor 10000 / 4096 \rfloor = 2 \quad (\text{Página Virtual } 2)$$

- **Deslocamento ($d$):** O resto da divisão.
$$d = 10000 \pmod{4096} = 1808 \quad (\text{Deslocamento } 1808)$$


**Passo 2: Consultar a Tabela de Páginas**
- Página $2 \rightarrow$ Mapeada na **Moldura $5$** ($f = 5$).


**Passo 3: Calcular o Endereço Físico**
$$\text{Endereço Físico} = (f \times \text{Tamanho da Página}) + d$$

$$\text{Endereço Físico} = (5 \times 4096) + 1808 = 20480 + 1808 = 22288$$

---
### Método 2: Tradução em Binário (Substituição Direta)
Se a questão der o endereço em bits, você não precisa fazer conta de divisão!

#### Regra do Número de Bits do Offset

$$\text{Número de bits do Offset} = \log_2(\text{Tamanho da Página em bytes})$$

- Página de $4\text{ KB}$ ($2^{12}\text{ bytes}$) $\rightarrow$ Os **12 bits da direita** são o Deslocamento ($d$).
 

#### Exemplo de Prova

> **Dados:**
> - Página de $4\text{ KB}$ (Offset utiliza os **12 bits** menos significativos).
> - Endereço Virtual em binário: `0010 000000000100` ($16\text{ bits}$).
> - A Página Virtual `0010` ($2$) mapeia para a Moldura Física `0111` ($7$).

**Passo 1: Separar os bits**
- Os 12 bits da direita são o Offset: `000000000100` ($d = 4$)
- Os bits restantes da esquerda são a Página Virtual: `0010` ($p = 2$)

**Passo 2: Substituir a Página pela Moldura**
- Troca-se `0010` por `0111` (Moldura $7$).

**Passo 3: Juntar o resultado**
- **Endereço Físico Binário:** `0111 000000000100`


---

# Tabela de páginas

> Estrutura mantida pelo Sistema Operacional (para cada processo) responsável por mapear os **endereços lógicos** (páginas) nos seus respetivos **endereços físicos** (quadros na RAM). Ou seja, é uma tabela de consulta com o endereço lógico e o físico correspondente de cada página 

A CPU gera endereços virtuais, que são traduzidos em endereços físicos por um componente de hardware chamado **MMU** (_Memory Management Unit_).

Quando o programa faz referência a um endereço de memória, a **MMU** consulta a tabela de páginas:
- Se a página já estiver carregada em uma moldura na RAM, a tradução é feita instantaneamente.
- Se a página estiver apenas no disco, a MMU dispara um evento de **Falta de Página (** **Page Fault** **)**. O Sistema Operacional paralisa a instrução, traz a página do disco para uma moldura da RAM e atualiza a tabela de mapeamento.

### Problemas das Tabelas de Páginas

1. **Tamanho:** Em sistemas de 32 bits com páginas de $4\text{ KB}$, cada processo precisa de $2^{20}$ entradas ($\approx 1\text{ milhão}$ de páginas), gerando tabelas gigantescas na memória.
    
2. **Desempenho:** Como quase toda instrução de CPU precisa acessar a memória, a tradução do endereço precisa acontecer em nanossegundos.


### Onde Guardar a Tabela de Páginas?

#### 1. Guardar nos Registradores da CPU

- **Vantagem:** Tradução ultra-rápida em nível de hardware.
    
- **Desvantagem:** **Troca de contexto péssima**. A cada mudança de processo, o SO precisa salvar e recarregar milhares de registradores, travando o sistema. Além disso, não há registradores suficientes para tabelas grandes.


#### 2. Guardar na Memória RAM

- **Vantagem:** **Troca de contexto ultra-rápida**. O SO precisa atualizar apenas $1$ registrador da CPU (o registrador base da tabela, como o `CR3`).
    
- **Desvantagem:** **Lentidão de acesso**. Toda instrução exige **2 acessos à RAM** (1º para ler a Tabela de Páginas + 2º para ler o dado real).

_(Nota de ajuste: Essa desvantagem da RAM é exatamente o motivo do surgimento da **TLB**, que funciona como um cache dos endereços mais usados para evitar essa segunda leitura na RAM)._

---
# Page Fault

> A **Falta de Página (_Page Fault_)** é uma interrupção gerada pelo hardware (MMU) quando um processo tenta acessar uma página lógica que **não está carregada na memória RAM física** naquele momento (o Bit de Presença na Tabela de Páginas está marcado como `0`).

  
1. **Acesso do Processo:** A CPU tenta ler ou escrever em um endereço de memória virtual.
    
2. **Detecção da Ausência:** A MMU consulta a Tabela de Páginas e percebe que o **Bit de Presença é `0`**.
    
3. **Interrupção (_Trap_):** A MMU interrompe a execução normal do programa e passa o controle para o Kernel do Sistema Operacional.
    
4. **Busca no Disco:** O SO localiza em qual setor da memória secundária (Disco/SSD ou área de _Swap_) essa página solicitada está armazenada.
    
5. **Alocação na RAM:** O SO encontra um **Quadro de Página (_Page Frame_) livre** na memória RAM.
    
    - _Se a RAM estiver cheia:_ O SO roda um **Algoritmo de Substituição de Páginas** (como FIFO, LRU, NRU) para escolher uma página "vítima", salvá-la no disco (se tiver sido alterada) e liberar o quadro.
        
6. **Carregamento:** O SO lê a página do disco e a copia para o quadro livre da RAM.
    
7. **Atualização da Tabela:** O SO atualiza a Tabela de Páginas do processo: escreve o número do quadro físico e altera o **Bit de Presença para `1`**.
    
8. **Reexecução da Instrução:** O SO devolve o controle à CPU, que recomeça a instrução que havia falhado (agora o dado já está na RAM e o acesso ocorre normalmente).


***DETALHES IMPORTANTES:***

- **Custo de Desempenho:** O _Page Fault_ é uma operação extremamente **lenta** se comparada à velocidade da CPU/RAM, pois exige acesso a disco (operadores de Entrada/Saída).

- **Thrashing (Hiperpaginação):** Se o sistema tiver pouca memória RAM física e muitos processos rodando, o SO passa mais tempo tratando _Page Faults_ e trocando páginas com o disco do que realmente executando os programas.

---

# TLB - Memória Associativa para Traduções
> Funciona como um cache das traduções mais recentes (página $\rightarrow$ moldura)

_Consultar a tabela de páginas na RAM a cada instrução deixaria o sistema lento. Para resolver isso, a MMU possui uma pequena memória associativa ultrarrápida chamada **TLB (Translation Lookaside Buffer)**_

- Se a tradução estiver na TLB (**TLB** **hit**), a conversão é imediata.
- Se não estiver (**TLB** **miss**), a MMU consulta a tabela de páginas na RAM e atualiza a TLB.

# Tabela multinível
> É **paginar a própria tabela de tradução**. A tabela é fatiada em páginas de $4\text{ KB}$ e espalhada pela RAM, sendo criada e carregada somente quando necessária.

---

# **Entrada de uma Tabela de Páginas** 

> (conhecida como **PTE** — _Page Table Entry_) é simplesmente uma única "linha" da tabela. Ela guarda o endereço do **Quadro Físico (Moldura)** na RAM e vários **bits de controle** essenciais para a segurança e o gerenciamento de memória.

  
### Representação Visual de uma PTE Típica

```
┌───────────────────────────────┬─────┬───────┬─────┬───────┬───────────┬─────┐
│  Número da Moldura Física (f) │ P/A │  R/W  │ U/S │ Dirty │ Referência│ PCD │
└───────────────────────────────┴─────┴───────┴─────┴───────┴───────────┴─────┘
```

### Os Campos e Bits de Controle 

1. **Número da Moldura Física ($f$ / _Frame Number_):**
    O campo principal da entrada. Armazena o endereço base do bloco físico na memória RAM onde aquela página está alocada.
    
2. **Bit de Presença / Ausência (P/A ou _Valid/Invalid_):**
      
    - `1`: A página está carregada na memória RAM física.
    - `0`: A página **não está na RAM** (está no disco). Acessá-la gera o gatilho de **Page Fault**.
    
3. **Bit de Modificação / Sujo (_Dirty Bit_ - D):**
    
    - `1`: O programa escreveu/alterou dados nessa página enquanto ela esteve na RAM.
    - `0`: A página foi apenas lida (está exatamente igual à cópia do disco).
        
    - _Para que serve?_ Se o SO precisar remover essa página da RAM, se estiver marcada como `1` ele **obrigatoriamente grava no disco**. Se estiver `0`, ele pode simplesmente **descartar** sem perder tempo gravando.
    
4. **Bit de Referência / Acesso (_Referenced Bit_ - R):**
      
    - `1`: A página foi acessada (seja para leitura ou escrita) recentemente.
        
    - _Para que serve?_ É usado diretamente pelos **Algoritmos de Substituição de Páginas** (como NRU e Segunda Chance/Relógio) para descobrir quais páginas estão sendo usadas ativamente e quais estão "esquecidas".
    
5. **Bits de Proteção / Permissão (R/W/X):**
    
    - Definem as permissões do processo sobre aquela página: **R**ead (Leitura), **W**rite (Escrita) e e**X**ecute (Execução de código). 
    
    - Tentar escrever em uma página marcada apenas como leitura gera uma interrupção de violação de acesso (_Segmentation Fault_).
    
6. **Bit de Modo (_User / Supervisor_ - U/S):**
    
    - Define o nível de privilégio necessário para ler ou alterar a página:
        - Modo Usuário (`User`): Qualquer programa pode acessar.
        - Modo Kernel (`Supervisor`): Apenas o código do Sistema Operacional tem permissão.
    
7. **Bit de Inibição de Cache (_Cache Disable_ - PCD):**
      
    - `1`: Desativa a memória Cache da CPU para esta página específica.
        
    - _Para que serve?_ Essencial quando a página mapeia hardware de Entrada/Saída (I/O), onde a leitura deve ser feita sempre em tempo real do periférico e não da memória cache.

---
