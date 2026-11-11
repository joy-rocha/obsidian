
No contexto de gerência de memória de um sistema operacional, esses três conceitos se referem a como o sistema lida com o tempo de acesso, a organização do espaço de armazenamento e a medição do uso do processador.

  

### 1. O que é a Espera (Wait / I/O Wait)

A **espera** ocorre quando um processo é pausado pelo sistema operacional porque precisa de dados da memória que ainda não estão prontos para uso.

  

Os dois cenários mais comuns de espera por memória são:

  

- **Falta de Página (_Page Fault_):** A memória RAM física é limitada. Por isso, o sistema usa o disco (HD ou SSD) como uma extensão da RAM (memória virtual/swap). Se um programa tenta acessar um dado que foi movido para o disco, ocorre uma "falta de página". O processo é colocado em estado de **espera (_blocked/waiting_)** enquanto o sistema operacional lê os dados do disco e os carrega de volta para a RAM.
    
      
    
- **Gargalo de I/O (Entrada/Saída):** A RAM é ordens de grandeza mais rápida do que o disco ou a rede. Quando a memória precisa ser preenchida com dados vindos de fora, a CPU suspende a execução daquele programa até que a transferência termine.
    
      
    

### 2. Como a Memória é Contada

A memória não é contada byte por byte individualmente, mas sim através de blocos e métricas de alocação:

  

#### A. Blocos Fundamentais (Páginas e Molduras)

O sistema operacional divide a memória em pedaços de tamanho fixo (geralmente de **4 KB**):

  

- **Páginas (_Pages_):** Blocos de memória no espaço **virtual** do programa.
    
      
    
- **Molduras (_Frames_):** Blocos reais na memória **física** (RAM). O sistema mantém uma tabela que mapeia qual página virtual corresponde a qual moldura física.
    
      
    

#### B. As Principais Métricas de Contagem

Ao olhar o uso de memória de um processo no Gerenciador de Tarefas ou comando `top`/`ps`, a contagem se divide em:

  

- **VSZ / VSS (_Virtual Memory Size_):** O tamanho total do espaço de endereçamento reservado pelo processo. Inclui RAM real, arquivos mapeados, bibliotecas e dados jogados no swap/disco.
    
      
    
- **RSS (_Resident Set Size_):** A quantidade real de memória RAM física que o processo está ocupando **neste instante**.
    
      
    
- **Memória Privada (_Private Memory_):** Memória exclusiva daquele programa (dados, variáveis e pilha).
    
      
    
- **Memória Compartilhada (_Shared Memory_):** Memória dividida entre vários programas (como bibliotecas do sistema `.dll` ou `.so`), evitando duplicar os mesmos dados na RAM.
    
      
    

### 3. O que são os "Tiques Ocupados" (_Clock Ticks / Jiffies_)

O sistema operacional não mede o tempo contínuo de forma direta; ele usa um relógio interno do hardware que gera interrupções periódicas (por exemplo, a cada 1 ou 10 milissegundos). Cada uma dessas interrupções é chamada de **tique (_clock tick_ ou _jiffy_)**.

  

**Tiques ocupados** referem-se à quantidade de tiques de relógio em que a CPU esteve trabalhando ativamente executando tarefas:

  

- **Tiques em Modo Usuário (_User Time_):** Tiques gastos executando o código do seu programa (cálculos, lógica do software).
    
      
    
- **Tiques em Modo Kernel/Sistema (_System Time_):** Tiques que a CPU gastou executando tarefas internas do sistema operacional — **incluindo a gerência de memória** (alocar memória, resolver _page faults_, mover dados para o swap).
    
      
    

#### A Relação entre Tiques e Memória:

Quando a memória RAM está esgotada, o sistema entra em um estado chamado **Thrashing** (trepidação). O sistema passa mais tempo trocando páginas de memória entre o disco e a RAM do que executando o programa.

  

Nessa situação, o gráfico de uso exibirá uma alta contagem de **tiques ocupados no Kernel/Sistema**, enquanto os tiques dedicados ao seu programa caem drasticamente, travando a máquina.



