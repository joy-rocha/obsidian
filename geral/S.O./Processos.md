**É um programa em execução**

Estes possuem:
 - **espaço de endereçamento**
 - **time-sharing** ou compartilhamento de tempo: é uma fatia de tempo dada a cada programa do computador, gerando a impressão de simultaneidade
 - **PC** (program counter): físico e presente individualmente em cada processo
 - **Escalonador**: organiza a ordem de prioridade e execução dos processos
# Criação de processos
Para que haja a criação de um processo, é preciso que um destes 4 eventos possíveis ocorra:
- Início de sistema
- Execução de uma chamada ao sistema de criação de um processo por outro processo
- Requisição do usuário
- Início de um job em lote

# Criação de processos no Linux/Unix
todos os processos são originados do `init()` a partir do comando `fork()`, cria uma cópia idêntica ao processo que chamou e em seguida executa outo comando, o `execve()` que substitui o conteúdo do processo pelo que interessa.

# Criação de processos no Windows
create process

# Término de processos
- saída normal (voluntária)
- saída por erro (voluntária)
- erro fatal (involuntária)
- cancelamento por outro processo (involuntária)

# Hierarquia de Processos : formato de árvore
- **Unix**: grupo de processos, init
- **Windows**: não há estrutura de hierarquia única e pode passar os filhos para outros processos 

# Estado dos Processos
 - **execução**: o processo está utilizando a CPU
 - **pronto**: pronto para executar, mas ainda esta na fila do escalonador
 - **bloqueado**: não está pronto para ser executado

![[Pasted image 20260801001711.png|285]]

