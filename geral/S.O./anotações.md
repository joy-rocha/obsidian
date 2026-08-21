- processos possuem um espaço de memoria diferente (não compartilham memória)

**ATENÇÃO:**
-tem o lance pra instalar lá, a biblioteca, o gcc tbm
-tem que aprender comandos de terminal linux
-estude a teoria e aplique a pratica no roteiro
-tudo que fizer lança no github

**PID** - código identificador do processo criado
**PPID** - código identificador do processo PAI 
# COMANDO USADOS NA AULA
**waith(NULL)** - espera que todos os procesos filhos acabarem pra chamar o pai
**fork()** - cria (duplica) um processo
**execlp()** - sobrescreve um codigo objeto em um determinado processo
**getpid()** - pega o identificador 
**getppid()** - pega o identificador do pai
**sleep()** - time de espera

./lab
cat /sla/status | -10
### **zumbi e órfão**
-  zumbi: é quando o filho terminou o que tinha que fazer mas o pai não chamou o wait, entt a droga do processo fica ocupando o espaço na tabela de processos

-  órfão: é quando o processo pai termina antes do filho




Com base na transcrição do vídeo **"Thread (entenda como sua aplicação funciona) // Dicionário do Programador"**, preparei um roteiro de estudos estruturado para guiar seu aprendizado sobre esse tema essencial da computação.

As marcações entre colchetes, como ****, indicam em qual parte do vídeo o assunto é abordado.

---

# 📚 Roteiro de Estudos: Entendendo Processos e Threads

## 1. Processos vs. Threads (A Base de Tudo)

Para entender como as aplicações funcionam no sistema operacional, o primeiro passo é distinguir essas duas entidades:

- **O que é um Processo?** É o conjunto organizacional que engloba instruções, dados e estados (como _sleeping_, _running_ ou _stopped_) ****. Quando você abre um programa e ele é carregado na memória, ele vira um ou mais processos ****. O sistema operacional aloca recursos como memória e espaço de endereço diretamente para o processo ****.
- **O que é uma Thread?** Traduzida do inglês como "fio" ou "linha", a thread é um pedaço ou subconjunto de um processo ****. Ela é a **menor unidade de processamento** e quem efetivamente executa as tarefas na CPU ****.
- **A relação prática:** A CPU não executa processos diretamente; ela precisa de uma thread para isso ****. Enquanto o processo serve para organizar e reservar recursos, as threads são as que "conversam" diretamente com o processador ****. Todo processo precisa ter ao menos uma thread para rodar ****.

---

## 2. Tipos de Threads

As threads podem rodar em diferentes níveis de privilégio no sistema operacional:

- **User Threads (Threads de Usuário):** Executam em uma camada acima do _kernel_ (núcleo do sistema) e não têm suporte direto dele ****. São as threads criadas e controladas diretamente pelas aplicações e jogos que nós, programadores, desenvolvemos ****.
- **Kernel Threads (Threads de Kernel):** São suportadas e gerenciadas diretamente pelo núcleo do sistema operacional ****. Elas permitem que o sistema execute tarefas simultâneas de nível mais baixo e atenda a chamadas de sistema ao mesmo tempo ****.

---

## 3. Concorrência vs. Paralelismo (O Grande Paradigm)

O vídeo traz uma excelente distinção prática utilizando as definições de Rob Pike (um dos criadores da linguagem Go):

- **Execução Sequencial (Monotarefa):** Fazer apenas uma coisa de cada vez de forma linear. Só entrega uma tarefa quando ela termina ****.
- **Concorrência:** É sobre **lidar com várias coisas ao mesmo tempo** ****. Duas ou mais threads podem iniciar, executar e concluir em períodos de tempo sobrepostos, intercalando sua execução na CPU ****. Em processadores com apenas um núcleo (_single-core_), isso gera um "paralelismo virtual", dando a ilusão de simultaneidade ****.
    - _Analogia do Vídeo:_ Um malabarista jogando várias bolas usando apenas uma mão (CPU single-core). Ele só toca em uma bola por vez, mas lida com todas de forma alternada e rápida ****. Está mais ligada ao nível de **software** ****.
- **Paralelismo:** É sobre **fazer várias coisas ao mesmo tempo** ****. Ocorre fisicamente quando temos processadores _multi-core_ (com múltiplos núcleos independentes que agem como computadores individuais, ex: _dual-core, quad-core, hexa-core_) ****.
    - _Analogia do Vídeo:_ Múltiplos malabaristas fazendo malabarismo de forma independente e simultânea ****. Está mais ligada ao nível de **hardware** ****.

---

## 4. Vantagens e Desafios do Multi-threading

Trabalhar com múltiplas threads traz grandes benefícios para a experiência do usuário, mas exige muito cuidado do desenvolvedor:

- **Os Benefícios:**
    - **Capacidade de resposta:** O software não trava enquanto executa tarefas pesadas em background ****.
    - **Compartilhamento e economia:** Compartilhar recursos dentro de um mesmo processo é muito mais rápido e econômico do que criar novos processos ****.
    - **Escalabilidade e Troca de Contexto:** Capacidade de distribuir tarefas na CPU e alternar entre elas de forma suave ****.
- **Os Desafios:**
    - Usar um processador moderno (_multi-core_) não garante que seu programa será mais rápido, a menos que ele tenha sido desenvolvido especificamente pensando em concorrência e distribuição de threads ****.

---

## 5. Programação Segura (Thread Safety)

Quando várias threads tentam acessar ou modificar a mesma informação na memória, ocorrem conflitos graves. Para programar de forma segura (_thread-safe_), o vídeo destaca três pontos essenciais:

- **Uso de Objetos Imutáveis:** Se os dados não mudam após serem criados, as threads não interferem umas nas outras (linguagens como Rust usam variáveis imutáveis por padrão para garantir segurança) ****.
- **Variáveis Locais:** As variáveis locais de um método são privadas e ficam armazenadas na pilha (_stack_) de execução de cada thread ****. Métodos que modificam apenas variáveis locais são automaticamente _thread-safe_ ****.
- **O Risco do Estado Compartilhado:** Se um método altera variáveis globais ou instâncias de objetos compartilhados fora do escopo local, haverá problemas se não houver um controle rigoroso (como semáforos ou travas) ****.

---

🧩 Que tal usarmos os conceitos de grafos e árvores do seu material para desenhar um mapa mental visual de como os processos gerenciam suas threads na memória?



Os seus materiais de leitura trazem um aprofundamento teórico muito rico, com detalhes técnicos específicos que complementam a visão geral apresentada no vídeo. Aqui estão as informações adicionais mais importantes divididas por tópicos:

### 1. Detalhes de Implementação de Threads (Espaço de Usuário vs. Kernel)

Enquanto o vídeo apenas cita que existem threads de usuário e de kernel, os slides detalham o funcionamento interno e as vantagens de cada modelo:

- **Threads em Espaço de Usuário:** São gerenciadas inteiramente por uma biblioteca em espaço de usuário, que possui sua própria **tabela de threads** para controlar registradores, estado e contador de programa (PC).
    - _Vantagens:_ A criação e a troca de contexto são extremamente rápidas porque **não exigem chamadas ao núcleo (syscalls)**. São ideais para tarefas intensivas de processamento (CPU-bound).
    - _Desvantagens:_ Se uma única thread fizer uma chamada de sistema bloqueante (como ler um arquivo do disco), **o sistema operacional bloqueia o processo inteiro**, parando todas as outras threads. Além disso, exige que a thread devolva o controle voluntariamente (`thread_yield`).
- **Threads em Espaço de Kernel:** O próprio núcleo do sistema operacional gerencia as threads por meio de uma tabela única.
    - _Vantagens:_ Excelente para programas com muita entrada e saída (I/O-bound), pois se uma thread bloquear aguardando o disco, o kernel escalona outra thread do mesmo processo.
    - _Desvantagens:_ A criação, a destruição e a troca de contexto são mais lentas devido ao custo de interrupção e transição para o modo kernel.

### 2. Estrutura de Memória e Ciclo de Vida de um Processo

Os materiais de leitura detalham minuciosamente a anatomia de um processo:

- **Espaço de Endereçamento:** O mapa de memória de um processo é dividido de forma organizada em **Texto** (o código compilado), **Variáveis Globais**, **Pilha** (onde ficam variáveis locais de funções e endereços de retorno) e **Descritores de Entrada/Saída** (arquivos abertos).
- **Criação de Processos:** Existem quatro eventos específicos que criam um processo: inicialização do sistema, requisição do usuário, chamada de sistema por outro processo e os processos de segundo plano chamados de **Daemons**.
- **Criação no Windows vs. Unix:** No Unix, o processo é clonado identicamente com `fork()` e depois modificado com `execve()`. No Windows, usa-se a chamada de sistema `CreateProcess`, que exige a passagem de **10 parâmetros** configuráveis de uma só vez.
- **Hierarquia:** No Unix, existe uma árvore hierárquica estrita sob o processo raiz `init`. No Windows, essa hierarquia não é rígida e um processo pai pode transferir a custódia de seus filhos para outro processo.

### 3. Classificações de Software e Objetivos do S.O.

As leituras iniciais categorizam os softwares de forma precisa:

- **Básico:** Software necessário para que o hardware funcione e para dar suporte a outros programas (como o Sistema Operacional, a BIOS e compiladores/interpretadores).
- **Aplicativo:** Softwares voltados para a atividade-fim do usuário, subdivididos em científicos/engenharia (com alto fator de precisão, ex: Matlab e Octave), programas web, de apoio educacional, militares ou de uso genérico (editores de texto, planilhas).
- **Utilitário:** São vistos como extensões do Sistema Operacional, realizando manutenção e otimização, como antivírus, compactadores de arquivos e gerenciadores de drivers.
- **Os Dois Grandes Objetivos do S.O.:** Atuar como um **gerenciador de recursos** de hardware e fornecer uma **abstração** amigável desse hardware para o desenvolvimento de programas.

### 4. Estruturas Organizacionais de um S.O.

Os conceitos fundamentam as diferentes arquiteturas de construção de um núcleo (kernel):

- **Sistemas Monolíticos:** Construídos de forma "bagunçada", onde todo o sistema é um conjunto de procedimentos interligados que podem chamar uns aos outros livremente.
- **Sistemas em Camadas:** O S.O. é dividido em níveis sobrepostos. O clássico sistema **THE** (de Dijkstra, 1968) possuía 6 camadas (de 0 a 5) organizando desde a alocação de CPU até os programas de usuário. O sistema **MULTICS** utilizava uma estrutura de anéis concêntricos de proteção.
- **Máquinas Virtuais:** Tecnologia que permite a execução de múltiplos Sistemas Operacionais simultaneamente no mesmo hardware.
- **Modelo Cliente-Servidor:** Transfere a maior parte das funções do S.O. para processos de usuário (servidores de arquivos, de impressão, etc.), deixando o kernel mínimo e robusto.

### 5. O Clássico Erro de Unidades de Medida

Uma observação fundamental nos conceitos básicos é a diferença de cálculo de grandezas:

- **Medidas de Memória:** Sempre calculadas em potência de 2. Logo, \(1\text{ KB} = 1024\text{ bytes}\).
- **Medidas de Transmissão e Outras Grandezas:** Sempre calculadas em potência de 10. Logo, uma rede de \(1\text{ Kbps}\) trafega exatamente \(1000\text{ bits por segundo}\).

🧠 Se quiser, podemos analisar o código do laboratório (como o `lab1_3.c` ou o `lab2_3.c`) para ver na prática como essa diferença de memória entre processos e threads funciona na sua tela.