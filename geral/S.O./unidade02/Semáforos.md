É basicamente tornar uma instrução indivisível e atômica, porque se as instruções forem separadas, a fatia de tempo pode acabar entre elas.

# Operaçoes Indivisíveis
#### Down: 
> verifica o semáforo <u>E</u> dorme caso seja 0 (zero) . Ele meio que subtrai 1
#### Up:
> incrementa o semáforo, ou seja, adiciona um sinal de acordar . Ele meio que soma 1

---
# 3  Tipos de Semáforos

#### MUTEX:
> Garante exclusão mútua, iniciado com 1 . Veja o ex

| if(mutex == 1){<br>	cont ++    <br>} | down(&mutex) | a vantagem é que torna a instrução atômica |
| ------------------------------------ | ------------ | ------------------------------------------ |
| 2 instruções                         | 1 instrução  |                                            |

#### FULL:
> Número de processos preenchidas, iniciado com zero

#### EMPITY:
> ...





---

Um semáforo funciona como um **contador inteiro interno** mantido pelo sistema operacional:
### 1. `sem_wait(&sem)` (Tentar/Esperar)

- **O que faz:** Tenta "pegar" uma autorização ou recurso.
- **Como funciona:**
    - Se o contador do semáforo for **maior que 0**: ele **subtrai 1** do contador e a _thread_ continua executando normalmente sem parar.
    - Se o contador for **igual a 0**: a _thread_ **bloqueia (dorme)** na CPU e fica esperando até que outra _thread_ aumente o contador.

### 2. `sem_post(&sem)` (Liberar/Avisar)

- **O que faz:** Devolve/libera um recurso ou envia um sinal de autorização.
- **Como funciona:**
    - Ele **soma 1** ao contador interno do semáforo.
    - Se houver alguma _thread_ dormindo/bloqueada no `sem_wait` daquele semáforo, o `sem_post` **acorda essa _thread_** para que ela possa continuar a execução.

|**Função**|**O que faz no contador**|**Se o valor for 0**|
|---|---|---|
|**`sem_wait`**|Decrementa (`-1`)|**Bloqueia/dorme** esperando um `sem_post`.|
|**`sem_post`**|Incrementa (`+1`)|Acorda quem estiver dormindo (ou deixa o crédito salvo).|

---

- **`wait()` / `notify()` (Java):** **Não possuem memória**
- **Semáforos (C / POSIX):** Possuem **estado/memória (contador interno)**

---
#### **Semáforo *Vs* Mutex**

| **Funcionalidade**      | **Semáforo**                       | **Mutex**                                     |
| ----------------------- | ---------------------------------- | --------------------------------------------- |
| **Valor do Contador**   | $0, 1, 2, 3, \dots, N$             | Apenas $0$ ou $1$                             |
| **Quem pode libertar?** | Qualquer _thread_ chama `sem_post` | **Apenas** a _thread_ que trancou             |
| **Objetivo principal**  | Controlo de recursos e sinalização | Proteger regiões críticas (dados partilhados) |



