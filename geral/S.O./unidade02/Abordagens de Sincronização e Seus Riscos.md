
---
### Desabilitar Interrupções
>O processo desativa as IRQs (Interrupt Requests) ao entrar na região crítica e as reabilita ao sair. Porém, permitir que processos em espaço de usuário (_user space_) desabilitem interrupções é perigoso porque um _bug_ ou processo malicioso pode nunca reabilitá-las, além de ser uma técnica ineficaz em sistemas com múltiplos núcleos de CPU Essa técnica é restrita ao próprio kernel em sistemas monoprocessados.
OU SEJA, no desabilitar interrupções é pra o processo desabilitar as interrupções ao entrar numa região critica pra que nenhum outro o interrompa até que termine, e por isso é perigoso você tem que esperar o tempo até que o processo termine pra outro poder rodar.

### Variável de Trava (Lock Variable) e Espera Ociosa
>Consiste em checar continuamente uma variável sinalizadora (ex: 0 para livre, 1 para ocupada). Espera Ociosa (Busy Waiting / Spinlock): O processo gasta ciclos de CPU continuamente em um loop apenas testando o valor da variável. Se o teste da variável e a sua alteração não forem executados como uma única instrução indivisível (atômica), dois processos podem ler 0 ao mesmo tempo e ambos entrarem na região crítica, violando a Regra 1.  

### Operações Não Atômicas e Condições de Corrida
>Instruções em linguagens de alto nível como cont++ ou cont-- são traduzidas pelo compilador em múltiplas instruções de máquina (ler da memória, modificar no registrador, escrever na memória). Se o escalonador interromper o processo no meio desse ciclo (troca de contexto), o valor final da variável ficará corrompido. Isso é chamado de condição de corrida (race condition).

### O Problema do Produtor-Consumidor
>Trata-se de um problema clássico de sincronização envolvendo dois processos que compartilham um buffer de tamanho fixo:

**Produtor**: Insere itens no buffer se houver espaço livre. Se estiver cheio, ele deve dormir (sleep).
**Consumidor**: Remove itens do buffer se houver itens disponíveis. Se estiver vazio, ele deve dormir (sleep).
**Sinalização** (wakeup): O produtor acorda o consumidor ao inserir o primeiro item; o consumidor acorda o produtor ao liberar o primeiro espaço.

**A Falha do sleep / wakeup ingênuo**
Usar apenas uma variável de controle cont com as primitivas sleep() e wakeup() falha devido à falta de atomicidade no acesso a cont.
Se o consumidor lê cont == 0 e, logo antes de executar o sleep(), ocorre uma troca de contexto para o produtor:

- O produtor insere um item, faz cont = 1 e envia um wakeup() para o consumidor.Como o consumidor ainda não dormiu de fato, o sinal de wakeup é perdido (lost wakeup).
- O consumidor volta a executar e dorme.
- O produtor eventualmente encherá o buffer e também dormirá. Ambos ficam bloqueados para sempre (deadlock).

Para resolver esse problema com segurança, utilizam-se abstrações como Semáforos (que mantêm contadores atômicos com fila de espera) ou Monitores.


---

==🔴**OBS**: é impossível prever quando a fatia de tempo vai acabar, por isso é muito difícil reproduzir um erro==

---

# Producer and Consumer - Detalhamento
possui um um buffer compartilhado de tamanho fixo ($N$)

#### Sleep e Wake-up (_Dormir e Acordar_)
São primitivas fornecidas pelo Sistema Operacional:
    - `sleep()`: suspende o processo que a chama, colocando-o no estado bloqueado
    - `wakeup(p)`: acorda o processo `p` que estava bloqueado

O objetivo principal do `sleep` e `wakeup` é **evitar a espera ociosa** (_busy waiting / spin lock_). Em vez de o processo gastar ciclos de CPU rodando um laço `while`, ele dorme até que a condição desejada (ex: ter vaga ou ter item no buffer) seja satisfeita

#### As 3 Regras do Problema:
1. O **Produtor** não pode inserir em um buffer cheio (deve esperar se não houver espaço)
2. O **Consumidor** não pode retirar de um buffer vazio (deve esperar se não houver itens)
3. **Exclusão Mútua:** Dois produtores ou dois consumidores não podem alterar as posições do buffer ao mesmo tempo

### A Solução Clássica com Semáforos 
Para resolver o Produtor-Consumidor em C (utilizando semáforos POSIX), utilizam-se **3 semáforos
1. **mutex** (inicial = 1): semáforo binário que garante a **exclusão mútua** no acesso ao buffer25.
2. **vazio** **/** **empty** (inicial = $N$): semáforo contador que indica quantas **posições livres** existem25.
3. **cheio** **/** **full** (inicial = 0): semáforo contador que indica quantos **itens estão prontos** para consumo

### Ordem Obrigatória dos Semáforos:
No código do **Produtor**, a ordem das chamadas é crítica:
```
sem_wait(&amp;vazio); // 1º: Espera haver espaço livre
sem_wait(&amp;mutex); // 2º: Entra na região crítica do buffer
// ... insere item no buffer ...
sem_post(&amp;mutex); // 3º: Sai da região crítica
sem_post(&amp;cheio); // 4º: Notifica que há um novo item disponível
```
