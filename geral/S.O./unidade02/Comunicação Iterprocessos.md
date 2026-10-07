**Em Sistemas Operacionais e Comunicação Interprocessos (IPC)**

# Estados do Processo
**Pronto (Ready):** 
>O processo possui todos os recursos necessários para rodar e está apenas aguardando a CPU ser alocada pelo escalonador.

**Execução (Running):** 
>O processo está ativamente utilizando a CPU no momento.

**Bloqueado (Blocked):** 
>O processo não pode rodar mesmo que a CPU esteja livre, pois aguarda um evento externo (como a conclusão de uma operação de I/O ou a liberação de um recurso).

---
# Conceitos principais:

### Condição de corrida (Race Condition)
> |Resultado do programa depende da ordem de intercalação (_interleaving_) das threads.|

### Recurso compartilhado
> É qualquer coisa que pode ser compartilhada ao mesmo tempo. ex: memória, arquivos...

### Exclusão Mútua
> Dois processos **NÃO** podem usar o recurso compartilhado ao mesmo tempo. A exclusão evita a condição de corrida

### Região Crítica
>  É a área de um programa que ta acessando a memória compartilhada

### Livelock 
>Os processos ou threads **não estão bloqueados** no estado _sleep_ (continuam rodando e gastando CPU), mas ficam presos em um ciclo de reações mútuas onde tentam, desistem e re-tentam continuamente sem que ninguém faça progresso real.

### Inanição (*starvation*) 
>O sistema como um todo continua funcionando e outros processos concluem suas tarefas, mas **um processo específico nunca consegue acessar o recurso compartilhado** porque outros processos continuam "furando a fila"

### *Lost wakeup* 
>Uma thread fica esperando por um sinal que nunca chega (ou chega para a thread errada), mesmo a condição pela qual ela espera já sendo verdadeira. Aí a thread que precisava ser acordada entra em _sleep_ esperando por um sinal que **já passou**, ficando bloqueada para sempre.

### Trava vazada 
>Um processo obtém um _lock_ ou semáforo (`down` / `acquire`), mas devido a uma falha de lógica no código (como um `return` precoce, um erro de exceção ou um esquecimento do programador), ele **nunca executa a liberação** (`up` / `release`). Daí a trava fica "vazada" na memória. A primeira thread a tentar pegar esse mesmo _lock_ depois ficará bloqueada indefinidamente

### **Espera Ociosa (** **Busy Waiting / Spin Lock** **)**
>É quando um processo fica preso em um laço contínuo (`while`) gastando ciclos de CPU testando uma variável até conseguir entrar na região crítica.

### **buffer** 
 >É uma área temporária de memória criada para armazenar dados durante a transferência entre dois processos, threads ou dispositivos de hardware (é uma proteção contra a variação da taxa de dados)
 
 ---

# 4 Critérios para *GARANTIR* a Exclusão Mútua
- Nunca dois processos podem estar simultaneamente em suas regiões críticas
- Nada pode ser levado em consideração sobre a velocidade da CPU ou a quantidade de processadores
- **Nenhum processo FORA da sua região crítica pode bloquear outros processos**
- Nenhum processo deve esperar eternamente para entrar na região crítica (espera finita)

---

# Condições de Coffman | As quatro condições **necessárias** para haver deadlock: 

- (1) **exclusão mútua** — o recurso só pode ser usado por uma thread por vez; 
- (2) **posse e espera** — a thread segura um recurso enquanto espera outro; 
- (3) **não preempção** — ninguém pode tomar à força o recurso de outra thread; 
- (4) **espera circular** — Existe uma corrente fechada (um ciclo) de processos em que cada um espera por um recurso mantido pelo próximo na fila. 
	- Existe um ciclo T1 → T2 → … → T1 de "espera por recurso de". Eliminar **qualquer uma** delas impede o deadlock. |

Para que ocorra um deadlock no sistema operacional, **todas as 4 condições precisam existir simultaneamente**. Se você conseguir quebrar ou eliminar **qualquer uma** delas, o deadlock se torna fisicamente impossível

---

# Test and Set Lock -> TSL
**ele é classificado na categoria de Soluções de Exclusão Mútua com Espera Ociosa com Suporte de Hardware.**

***tem espera ociosa e perde tempo (bease wait) , MAS resolve a exclusão mútua e tem o auxílio do hardware.*** 
- **Espera Ociosa (_Busy Waiting / Spin Lock_):**  Quem tenta entrar e encontra a trava ocupada fica preso em um laço `while` gastando ciclos de CPU.
- **Garante a Exclusão Mútua:**  Ao contrário da variável de trava pura em software, o TSL resolve o problema de dois processos entrarem juntos na região crítica.
- **Auxílio de Hardware:** O TSL é uma instrução de montador/código de máquina fornecida diretamente pelo processador.

### Como o TSL funciona de verdade (para a prova)

A instrução `TSL RX, LOCK` faz duas coisas de forma **atômica** (indivisível) em nível de hardware:
1. Lê o valor da variável de memória `LOCK` para o registrador `RX`.
2. Escreve um valor diferente de zero (ex: `1`) na memória `LOCK`.

Como o processador **tranca o barramento de memória** enquanto executa essa instrução, nenhuma outra CPU ou dispositivo consegue acessar aquela posição de memória naquele exato instante. Isso impede a condição de corrida no momento do teste da trava, sem precisar mexer nas interrupções do sistema.

---
==SUGESTÃO DE LEITURA==
pág 82-89






