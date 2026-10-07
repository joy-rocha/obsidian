>**É basicamente tornar uma instrução indivisível e atômica, porque se as instruções forem separadas, a fatia de tempo pode acabar entre elas.**

A grande inovação proposta por Dijkstra em 1965 foi tornar a verificação do valor e o bloqueio/alteração uma **operação atômica**

---
# Operaçoes Indivisíveis
#### - down()/sem_wait() **: 
> Decrementa o contador; se o valor for 0, o processo/thread é colocado para dormir
> (verifica o semáforo <u>E</u> dorme caso seja 0 (zero) . Ele meio que subtrai 1)
#### - up() / sem_post():
> Incrementa o contador e, se houver processos dormindo, acorda um deles
> (incrementa o semáforo, ou seja, adiciona um sinal de acordar . Ele meio que soma 1)

|**Função**|**O que faz no contador**|**Se o valor for 0**|
|---|---|---|
|**`sem_wait`**|Decrementa (`-1`)|**Bloqueia/dorme** esperando um `sem_post`.|
|**`sem_post`**|Incrementa (`+1`)|Acorda quem estiver dormindo (ou deixa o crédito salvo).|

---
# 3  Tipos de Semáforos
### MUTEX
>Semáforo binário iniciado em **1**, responsável por garantir a exclusão mútua na região crítica do buffer

| if(mutex == 1){<br>	cont ++    <br>} | down(&mutex) | a vantagem é que torna a instrução atômica |
| ------------------------------------ | ------------ | ------------------------------------------ |
| 2 instruções                         | 1 instrução  |                                            |

### FULL (Cheio)
>Semáforo contador iniciado em **0**, indica a quantidade de itens prontos para consumo

### EMPTY (Vazio)
>Semáforo contador iniciado em $N$ (capacidade total do buffer). Indica a quantidade de **posições livres** disponíveis para o produtor inserir novos dados


---

# Java (`wait`/`notify`) vs. Semáforos (C)

>Em **Java (Monitores)**, o `notify()` só acorda uma thread se ela já estiver dormindo no `wait()` naquele exato instante. Se nenhuma thread estiver esperando, a notificação é **perdida** (_lost wakeup_)

>Em **C (Semáforos POSIX)**, a operação `sem_post` incrementa o contador atômico. Esse valor **fica salvo na memória do semáforo**, servindo como autorização para um `sem_wait` futuro.

- **`wait()` / `notify()` (Java):** **Não possuem memória**
- **Semáforos (C / POSIX):** Possuem **estado/memória (contador interno)**

---
# Semáforo *Vs* Mutex

| **Funcionalidade**      | **Semáforo**                       | **Mutex**                                     |
| ----------------------- | ---------------------------------- | --------------------------------------------- |
| **Valor do Contador**   | $0, 1, 2, 3, \dots, N$             | Apenas $0$ ou $1$                             |
| **Quem pode libertar?** | Qualquer _thread_ chama `sem_post` | **Apenas** a _thread_ que trancou             |
| **Objetivo principal**  | Controlo de recursos e sinalização | Proteger regiões críticas (dados partilhados) |


- **Mutex:** Tem conceito de posse (_ownership_). **Apenas a thread que trancou o mutex pode destravá-lo.**
- **Semáforo:** Não tem conceito de dono. Qualquer thread pode chamar `sem_post()`, o que o torna ideal também para **sinalização e sincronização de eventos** entre threads diferentes (ex: o produtor acorda o consumidor).
