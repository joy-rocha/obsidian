Estados do Processo
A transição entre o uso do processador e o aguardo por recursos é gerenciada através de três estados fundamentais:

Pronto (Ready): O processo possui todos os recursos necessários para rodar e está apenas aguardando a CPU ser alocada pelo escalonador.
Execução (Running): O processo está ativamente utilizando a CPU no momento.
Bloqueado (Blocked): O processo não pode rodar mesmo que a CPU esteja livre, pois aguarda um evento externo (como a conclusão de uma operação de I/O ou a liberação de um recurso).
As 4 Regras da Região Crítica
Para garantir uma solução correta para o problema da exclusão mútua sem gerar falhas no sistema, a implementação deve respeitar estritamente quatro condições:

Exclusão Mútua: Dois processos nunca podem estar simultaneamente dentro de suas regiões críticas.
Independência de Hardware: Nenhuma suposição pode ser feita sobre a velocidade das CPUs ou o número de processadores/memória RAM disponível.
Sem Bloqueio Externo: Um processo rodando fora de sua região crítica não pode bloquear nenhum outro processo de entrar na região crítica.      
Espera Finita: Nenhum processo pode esperar eternamente para entrar em sua região crítica (ausência de starvation e deadlock).
Abordagens de Sincronização e Seus Riscos
1. Desabilitar Interrupções
O processo desativa as IRQs (Interrupt Requests) ao entrar na região crítica e as reabilita ao sair.

Problema: Se for permitido que processos em espaço de usuário (user space) façam isso, um programa malicioso ou com bug (ex: um loop infinito) pode nunca reabilitar as interrupções, travando todo o sistema operacional. Essa técnica é restrita ao próprio kernel em sistemas monoprocessados.
2. Variável de Trava (Lock Variable) e Espera Ociosa
Consiste em checar continuamente uma variável sinalizadora (ex: 0 para livre, 1 para ocupada).

Espera Ociosa (Busy Waiting / Spinlock): O processo gasta ciclos de CPU continuamente em um loop apenas testando o valor da variável.
Falha de Atomicidade: Se o teste da variável e a sua alteração não forem executados como uma única instrução indivisível (atômica), dois processos podem ler 0 ao mesmo tempo e ambos entrarem na região crítica, violando a Regra 1.  
3. Operações Não Atômicas e Condições de Corrida
Instruções em linguagens de alto nível como cont++ ou cont-- são traduzidas pelo compilador em múltiplas instruções de máquina (ler da memória, modificar no registrador, escrever na memória).

Se o escalonador interromper o processo no meio desse ciclo (troca de contexto), o valor final da variável ficará corrompido. Isso é chamado de condição de corrida (race condition).
O Problema do Produtor-Consumidor
Trata-se de um problema clássico de sincronização envolvendo dois processos que compartilham um buffer de tamanho fixo:

Produtor: Insere itens no buffer se houver espaço livre. Se estiver cheio, ele deve dormir (sleep).
Consumidor: Remove itens do buffer se houver itens disponíveis. Se estiver vazio, ele deve dormir (sleep).
Sinalização (wakeup): O produtor acorda o consumidor ao inserir o primeiro item; o consumidor acorda o produtor ao liberar o primeiro espaço.
A Falha do sleep / wakeup ingênuo
Usar apenas uma variável de controle cont com as primitivas sleep() e wakeup() falha devido à falta de atomicidade no acesso a cont.
Se o consumidor lê cont == 0 e, logo antes de executar o sleep(), ocorre uma troca de contexto para o produtor:

O produtor insere um item, faz cont = 1 e envia um wakeup() para o consumidor.
Como o consumidor ainda não dormiu de fato, o sinal de wakeup é perdido (lost wakeup).
O consumidor volta a executar e dorme.
O produtor eventualmente encherá o buffer e também dormirá. Ambos ficam bloqueados para sempre (deadlock).
Para resolver esse problema com segurança, utilizam-se abstrações como Semáforos (que mantêm contadores atômicos com fila de espera) ou Monitores.

# Respaotas do Roteiro
1) o único que tem alterações é o "nunca consumidos" . sim. consumidos = 20, produzidos= 20, esperado = 20, nunca consumidos tivemos 9, 12, 4.
OU SEJA:
	Não, o resultado varia a cada execução (ex: "nunca consumidos" oscilou em 9, 12 e 4). Isso demonstra comportamento não-determinístico (condição de corrida), pois sem sincronização a ordem de execução das threads depende unicamente das trocas de contexto feitas pelo escalonador da CPU.

2) por que o processo tenta consumir quando não há itens e tenta criar quando o buffer está cheio, devido a falta de tomicidade das instruções fazendo com que a operação seja feita com a variável de controle desatualizada, sobrescrevendo valores sem certeza 
OU SEJA:
	O programa está incorreto pelos seguintes motivos:
		- Falta de Exclusão Mútua: Duas threads podem ler o mesmo índice de buffer.fim ao mesmo tempo, fazendo com que um item seja sobrescrito e perdido.
		- Operações Não Atômicas: Ações como buffer.fim = (buffer.fim + 1) % TAM_BUFFER exigem múltiplos passos na CPU. Uma interrupção no meio do caminho faz a thread seguinte trabalhar com dados desatualizados
		- Falta de Sinalização Condicional: Produtores continuam tentando escrever com o buffer cheio e consumidores tentando ler com o buffer vazio.

3) tudo ok

4) A ordem é obrigatória para evitar um deadlock (impasse):
	Inversão incorreta (mutex antes de vazio): Se o buffer estiver cheio, o produtor adquire o mutex (tranca o acesso ao buffer) e depois bloqueia em sem_wait(&vazio) à espera de espaço.
	O problema: O produtor vai dormir segurando a tranca (mutex). Quando o consumidor tentar retirar um item para liberar espaço, ele ficará preso tentando adquirir o mesmo mutex.
	Resultado: O produtor espera pelo consumidor (para liberar espaço) e o consumidor espera pelo produtor (para liberar o mutex), travando o programa para sempre.


CONTINUAR DA 6.3

5) - **Resultado:** O resultado continua correto (0 erros e 0 itens não consumidos).
	- **O que mudou:** Mudou apenas o **desempenho** (o tempo total de execução aumentou bastante), mas a **corretude** permaneceu intacta.
	- **Explicação:** O `mutex` garante exclusão mútua rigorosa. Mesmo que a thread "durma" dentro da seção crítica, nenhuma outra thread consegue entrar para alterar os dados. Colocar um atraso/sleep na seção crítica prejudica o desempenho, mas não corrompe os dados.

6) **Resultado:** O programa **trava** (entra em _deadlock_) assim que o buffer enche.
	- **Cenário / Dependência Circular:**
    1. O produtor P1 pega o `mutex` (tranca o buffer).
    2. Com o buffer cheio (`vazio == 0`), P1 tenta executar `sem_wait(&vazio)` e **bloqueia segurando o mutex**.
    3. O consumidor precisa retirar um item para liberar espaço (`vazio`), mas para retirar um item ele precisa primeiro do `mutex`.
    4. **Dependência Circular:** O produtor espera o consumidor liberar espaço, enquanto o consumidor espera o produtor liberar o `mutex`. Ninguém anda

7) **Resultado:** Apenas **1 item** é produzido antes de o programa travar totalmente.
	- **Explicação:** O produtor insere o primeiro item e finaliza a função sem soltar o `mutex`. Na iteração seguinte (ou quando o consumidor tenta ler), qualquer thread que chamar `sem_wait(&mutex)` ficará bloqueada para sempre, pois a chave do `mutex` nunca foi devolvida.

8) - **Resultado:** Sim, o programa volta a **corromper dados** (mensagens de erro de item consumido em duplicata e contador fora do intervalo).
    
	- **Explicação:** Os semáforos `vazio` e `cheio` garantem apenas a **sincronização de condição** (não ler de buffer vazio e não escrever em buffer cheio). Porém, quando há múltiplos produtores (ou múltiplos consumidores), dois produtores podem executar o código de inserção simultaneamente, acessando o mesmo índice `buffer.fim` sem **exclusão mútua**.

9) - **Resultado:** O atraso tornou a condição de corrida **muito mais fácil e frequente de observar**.
    
	- **Explicação:** O atraso aumenta o tempo em que as threads permanecem ativas na CPU e "amplia a janela de tempo" para que o escalonador interrompa uma thread no meio de uma operação não atômica, forçando a intercalação prejudicial entre elas

10) comparar os resultados com o gabarito
# parte em JAVA

11) - **O que acontece:** Sim, acontecem **os mesmos erros do C** (itens consumidos em duplicata, contador do buffer corrompido ou saindo do intervalo [0, 5]).
    
	- **Por que acontece:** Porque o problema é de **concorrência e acesso à memória compartilhada**, e não da linguagem de programação usada. Seja em C ou em Java, quando duas threads tentam alterar a mesma variável no mesmo milissegundo sem proteção (exclusão mútua), o processador faz operações desatualizadas e corrompe os dados (condição de corrida)



**Pergunta 11 (Sem sincronização em Java)**
- **Resposta:** Sim, surgem exatamente os mesmos erros da versão em C (itens duplicados e contador fora do intervalo $[0, 5]$). Isso ocorre porque a condição de corrida e a falta de exclusão mútua são problemas conceituais de concorrência em memória compartilhada no nível da CPU, independente de ser executado em C ou Java.
    

**Pergunta 12 (Validação dos TODOs preenchidos)**
- **Resposta:** O programa executa com **0 erros** e **0 itens não consumidos**. A adição de `synchronized`, `wait()` e `notifyAll()` garantiu com sucesso a exclusão mútua e a sincronização por condição.
    

**Pergunta 13 (Exigência do `synchronized` para `wait`/`notifyAll`)**
- **Resposta:** A exigência existe para garantir atomicidade e evitar _lost wakeup_. A chamada `wait()` precisa **liberar a trava (_lock_) do objeto e dormir em um único passo atômico**. Sem a trava, ocorreria uma condição de corrida no momento em que a thread verifica a condição e decide dormir. Em C, os semáforos são primitivas do sistema operacional com contadores atômicos próprios e não dependem de uma estrutura de classe.
    

**Pergunta 14 (Experimento E6 - Atraso dentro do monitor)**
- **Resposta:** O resultado **continua correto**. Colocar o `sleep` dentro da seção `synchronized` é seguro quanto à corretude porque a thread **não solta a trava do monitor** enquanto dorme, impedindo acessos simultâneos. Porém, isso é péssimo para o desempenho, pois segura o recurso e força todas as outras threads a ficarem bloqueadas aguardando a trava.
    

**Pergunta 15 (Experimento E7 - Trocar `while` por `if`)**
- **Resposta:** Ocorrem erros de consistência ou a exceção `ArrayIndexOutOfBoundsException`. Quando o `notifyAll()` acorda várias threads de uma vez, elas entram na fila para executar uma de cada vez. Com o `if`, a segunda thread a ser executada **não retesta a condição** e assume que o espaço ainda está disponível, mesmo que a primeira thread a acordar já o tenha ocupado.
    

**Pergunta 16 (Experimento E8 - Usar `notify()` em vez de `notifyAll()`)**
- **Resposta:** O programa **trava em _deadlock_**. Como o Java possui apenas uma fila de espera por objeto, o `notify()` acorda uma thread aleatória. Se um produtor acorda outro produtor (em vez de um consumidor) quando o buffer está cheio, a thread acordada volta a dormir e o sinal é perdido (_lost wakeup_). O `notifyAll()` resolve isso pois garante que a thread do tipo correto seja acordada, mesmo ao custo de acordar threads que apenas voltarão a dormir no `while`.
    

**Pergunta 17 (Experimento E9 - Remover `synchronized` de apenas um método)**
- **Resposta:** **Não garante exclusão mútua**. A thread que chama o método sem `synchronized` (`remover()`) entra direto na execução sem pedir a chave do objeto. Dessa forma, ela pode modificar os dados ao mesmo tempo em que outro método `synchronized` está sendo executado. Todos os métodos que tocam no estado compartilhado precisam do modificador.
    

**Pergunta 18 (Comparação com a solução de referência)**
- **Resposta:** Não há diferenças relevantes em relação à solução de referência. Ambas empregam `synchronized` nos métodos de inserção e remoção, usam laços `while` com `wait()` para tratar espera condicional e disparam `notifyAll()` ao alterar o estado do buffer.

### 8.2 Preenchendo os TODOs

Abra [`java/base/Buffer.java`](java/base/Buffer.java) e resolva os TODOs 1 a 6:

|TODO|O que fazer|Onde|
|---|---|---|
|1|Adicionar `synchronized` à assinatura de `inserir`|`public synchronized void inserir(int item)`|
|2|`while (contador == capacidade) { wait(); }`|início do corpo de `inserir`|
|3|`notifyAll();`|final do corpo de `inserir`|
|4|Adicionar `synchronized` à assinatura de `remover`|`public synchronized int remover()`|
|5|`while (contador == 0) { wait(); }`|início do corpo de `remover`|
|6|`notifyAll();`|antes do `return item;` em `remover`|

Recompile e rode:
# Laboratório: Comunicação Interprocessos — Semáforos (C) e Monitores (Java)

## 1. Objetivos

Ao final deste laboratório você deve ser capaz de:

1. Explicar o que é uma **seção crítica**, uma **condição de corrida** (_race condition_) e por que exclusão mútua sozinha não resolve o problema do produtor-consumidor.
2. Implementar o problema do **produtor-consumidor** com **semáforos POSIX** em C (`sem_t`, `sem_wait`, `sem_post`).
3. Implementar o mesmo problema com **monitores** em Java (`synchronized`, `wait()`, `notifyAll()`).
4. Provocar **deliberadamente** erros clássicos de sincronização (condição de corrida, deadlock, _lost wakeup_) inserindo atrasos e alterando a ordem de instruções, e explicar por que cada erro acontece.
5. Comparar as duas abordagens (semáforos x monitores) em termos de expressividade, segurança e propensão a erros.

## 2. Pré-requisitos

- **C**: `gcc` com suporte a `pthread` e `<semaphore.h>` (POSIX). No Windows, use **WSL**, uma máquina Linux ou um container Docker — a API POSIX de semáforos não está disponível nativamente no MSVC/MinGW puro.
- **Java**: JDK 11+ (`javac`, `java`).
- Terminal com acesso a `gcc`/`make` e `javac`/`java`.
- Faça download do [repositório](https://github.com/henriquecunha2/lab-ipc-semaforos-monitores)

## 3. Conceitos-chave (revisão rápida)

|Conceito|Definição|
|---|---|
|Seção crítica|Trecho de código que acessa um recurso compartilhado e não pode ser executado por mais de uma thread ao mesmo tempo.|
|Condição de corrida|Resultado do programa depende da ordem de intercalação (_interleaving_) das threads.|
|Semáforo|Variável inteira controlada apenas por `wait`/`sem_wait` (decrementa, bloqueia se < 0) e `signal`/`sem_post` (incrementa). Não tem noção de “dono”: qualquer thread pode liberar.|
|Monitor|Construção de mais alto nível que embute a exclusão mútua na própria estrutura (classe): apenas uma thread executa por vez dentro de um método `synchronized`. Usa **variáveis de condição** (`wait`/`notify`/`notifyAll`) para esperar por um estado específico.|
|_Lost wakeup_|Uma thread perde um sinal de `notify`/`sem_post` porque ainda não estava esperando quando o sinal foi enviado, ou porque o sinal foi “roubado” por outra thread.|

## 4. Estrutura do repositório

```
lab-ipc-semaforos-monitores/
├── README.md                     <- este roteiro
├── RELATORIO_TEMPLATE.md         <- modelo do relatório a entregar
├── c/
│   ├── Makefile
│   ├── base/produtor_consumidor.c    <- código base (TODO 0 a TODO 9)
│   └── solucao/produtor_consumidor.c <- solução de referência
└── java/
    ├── base/    (Buffer.java com TODO 1 a TODO 6, Produtor.java, Consumidor.java, Main.java)
    └── solucao/ (versão resolvida)
```

Em ambas as linguagens, o cenário é o mesmo: **2 produtores**, cada um gerando **10 itens** (sequenciais, de 0 a 19), **2 consumidores**, e um **buffer circular de capacidade 5**. O código já inclui uma verificação automática (impressa no console) que **não faz parte do exercício de sincronização** — ela só serve para você enxergar quando algo deu errado: contagem de itens consumidos em duplicata, itens nunca consumidos, e o valor do contador interno do buffer saindo do intervalo `[0, capacidade]`.

---

## 5. Parte 1 — O problema do produtor-consumidor

Um ou mais **produtores** inserem itens em um **buffer** de tamanho fixo; um ou mais **consumidores** retiram itens desse buffer. Três regras precisam ser garantidas:

1. **Produtor não pode inserir em buffer cheio** — deve esperar até haver espaço.
2. **Consumidor não pode retirar de buffer vazio** — deve esperar até haver item.

Com semáforos, isso é resolvido classicamente com **três semáforos**:

- `mutex` (binário, inicial 1) — exclusão mútua.
    1. **Exclusão mútua**: nunca duas threads podem alterar o buffer ao mesmo tempo.
- `vazio` (contagem, inicial = capacidade) — quantas posições livres existem.
- `cheio` (contagem, inicial = 0) — quantos itens prontos para consumo existem.

Com monitores, a exclusão mútua já vem “de graça” com `synchronized`; o que precisa ser feito manualmente é a **espera condicional** com `wait()`/`notifyAll()` dentro do método sincronizado.

---

## 6. Parte 2 — C com semáforos POSIX

### 6.1 Rodando o código base sem nenhuma sincronização

Antes de mexer em qualquer coisa, compile e rode o código base **exatamente como está** (os TODOs ainda não foram preenchidos, então não há sincronização nenhuma):

```bash
cd c
make base
./base/pc
```

> **Pergunta 1.** Rode o programa 3 a 5 vezes seguidas. O resultado final (`Total produzido`, `Total consumido`, `Nunca consumidos`, `buffer.contador final`) é sempre o mesmo? Você viu alguma mensagem `*** ERRO ***`? Anote os valores obtidos em cada execução.
> 
> **Pergunta 2.** Mesmo sem ver uma mensagem de erro explícita, por que o programa está incorreto? (Dica: pense no que aconteceria se duas threads executarem `buffer.dados[buffer.fim] = item; buffer.fim = (buffer.fim + 1) % TAM_BUFFER;` ao mesmo tempo, cada uma lendo o mesmo valor antigo de `buffer.fim`.)

### 6.2 Preenchendo os TODOs

Abra [`c/base/produtor_consumidor.c`](c/base/produtor_consumidor.c) e resolva, **nesta ordem**, os TODOs 0 a 9:

|TODO|O que fazer|Onde|
|---|---|---|
|0 / 0b|Declarar (`sem_t mutex, vazio, cheio;`) e inicializar (`sem_init`) os três semáforos|topo do arquivo / início de `main`|
|1|`sem_wait(&vazio)` antes de inserir|início do laço em `produtor`|
|2|`sem_wait(&mutex)` antes de inserir|logo após o TODO 1|
|3|`sem_post(&mutex)` depois de inserir|logo após `inserir_item(...)`|
|4|`sem_post(&cheio)` depois de liberar o mutex|logo após o TODO 3|
|5|`sem_wait(&cheio)` antes de remover|início do laço em `consumidor`|
|6|`sem_wait(&mutex)` antes de remover|logo após o TODO 5|
|7|`sem_post(&mutex)` depois de remover|logo após `remover_item(...)`|
|8|`sem_post(&vazio)` depois de liberar o mutex|logo após o TODO 7|
|9|`sem_destroy` nos três semáforos (boa prática)|final de `main`|

Valores iniciais corretos (TODO 0b): `mutex = 1`, `vazio = TAM_BUFFER`, `cheio = 0`.

Compile e rode novamente:

```bash
make base
./base/pc
```

> **Pergunta 3.** Rode 5 vezes. `Nunca consumidos` deve ser sempre 0 e nenhuma mensagem `*** ERRO ***` deve aparecer. Se ainda aparecer erro, revise a ordem dos seus `sem_wait`/`sem_post` antes de continuar.
> 
> **Pergunta 4.** Por que a ordem `sem_wait(&vazio)` **antes de** `sem_wait(&mutex)` é obrigatória, e não o contrário? Você vai testar a ordem trocada no experimento 2.4-E2 abaixo — tente prever o que vai acontecer antes de testar.

### 6.3 Experimentos — inserindo atrasos e provocando erros

Use sempre o mesmo procedimento: faça a alteração indicada, rode `make base && ./base/pc` (ou recompile o arquivo indicado), registre o resultado no relatório (Seção “Experimentos” do `RELATORIO_TEMPLATE.md`) e **restaure o código original antes de passar para o próximo experimento**.

**E1 — Atraso dentro da seção crítica, com sincronização correta.**  
No arquivo resolvido (`c/base/produtor_consumidor.c`, já com os TODOs preenchidos), mude:

```c
#define ATRASO_PRODUTOR_US0
```

para

```c
#define ATRASO_PRODUTOR_US50000/* 50 ms */
```

Rode o programa.

> **Pergunta 5.** O resultado final continua correto (0 erros, 0 itens nunca consumidos)? O que mudou então — corretude ou desempenho (tempo total de execução)? Explique por que um atraso _dentro_ da seção crítica protegida por `mutex` não pode gerar corrupção de dados, mesmo sendo “perigoso” colocar um `sleep` ali.

**E2 — Trocar a ordem de `vazio` e `mutex` no produtor (deadlock).**  
Ainda no código com TODOs preenchidos, troque a ordem das linhas no `produtor`:

```c
sem_wait(&mutex);
sem_wait(&vazio);
```

(mutex antes de vazio). Rode o programa com `TAM_BUFFER` pequeno (já é 5) e mais itens, se necessário, para forçar o buffer a encher.

> **Pergunta 6.** O programa termina ou trava (fica parado, sem imprimir mais nada)? Reconstrua o cenário passo a passo: produtor P1 pega `mutex`, buffer está cheio, P1 bloqueia em `sem_wait(&vazio)` **segurando o mutex**. O que acontece com o consumidor que precisa do `mutex` para liberar espaço? Desenhe (no relatório) o diagrama de dependência circular que caracteriza esse deadlock.

**E3 — Esquecer de liberar o mutex.**  
Comente a linha `sem_post(&mutex);` (TODO 3) apenas no `produtor`.

> **Pergunta 7.** O que acontece após o primeiro item ser produzido? Quantos itens, no total, chegam a ser produzidos antes do programa travar completamente? Por quê exatamente esse número?

**E4 — Remover o mutex, mantendo `vazio`/`cheio`.**  
Comente `sem_wait(&mutex)`/`sem_post(&mutex)` nos quatro pontos (produtor e consumidor), mas **mantenha** `vazio`/`cheio` funcionando.

> **Pergunta 8.** Mesmo com `vazio` e `cheio` corretos (o buffer nunca fica cheio além da capacidade nem é lido vazio), o programa ainda pode corromper dados? Rode 5 vezes e veja se aparece `*** ERRO: item X CONSUMIDO EM DUPLICATA ***` ou `buffer.contador` fora do intervalo. Explique por que `vazio`/`cheio` sozinhos não bastam quando há **mais de um produtor** (ou mais de um consumidor).

**E5 — Atraso sem nenhuma sincronização.**  
Volte ao **código base original** (sem nenhum TODO preenchido) e ajuste:

```c
#define ATRASO_PRODUTOR_US20000
#define ATRASO_CONSUMIDOR_US20000
```

> **Pergunta 9.** Compare com a Pergunta 1/2 (mesmo código, sem atraso). O atraso tornou a condição de corrida **mais fácil ou mais difícil** de observar? Por que introduzir atrasos artificiais é uma técnica válida para “ampliar a janela” de uma condição de corrida durante testes?

---

## 7. Parte 3 — Consultando a solução de referência (C)

Depois de concluir a Parte 2, compare sua solução com [`c/solucao/produtor_consumidor.c`](c/solucao/produtor_consumidor.c):

```bash
make solucao
./solucao/pc
```

> **Pergunta 10.** Existe alguma diferença relevante entre a sua solução e a solução de referência (além de nomes de variáveis)? Se sim, qual, e as duas abordagens são igualmente corretas?

---

## 8. Parte 4 — Java com monitores

### 8.1 Rodando o código base sem sincronização

```bash
cd java/base
javac *.java
java Main
```

> **Pergunta 11.** Rode 3 a 5 vezes. Compare o comportamento com o código C base (Parte 2.1): os mesmos tipos de erro aparecem (itens em duplicata, `contador` fora do intervalo)? Por que era esperado que sim, já que o problema de fundo (acesso concorrente sem exclusão mútua) é o mesmo, independentemente da linguagem?

### 8.2 Preenchendo os TODOs

Abra [`java/base/Buffer.java`](java/base/Buffer.java) e resolva os TODOs 1 a 6:

|TODO|O que fazer|Onde|
|---|---|---|
|1|Adicionar `synchronized` à assinatura de `inserir`|`public synchronized void inserir(int item)`|
|2|`while (contador == capacidade) { wait(); }`|início do corpo de `inserir`|
|3|`notifyAll();`|final do corpo de `inserir`|
|4|Adicionar `synchronized` à assinatura de `remover`|`public synchronized int remover()`|
|5|`while (contador == 0) { wait(); }`|início do corpo de `remover`|
|6|`notifyAll();`|antes do `return item;` em `remover`|

Recompile e rode:

> **Pergunta 12.** Rode 5 vezes. `Nunca consumidos` deve ser sempre 0 e nenhum `*** ERRO ***` deve aparecer.
> 
> **Pergunta 13.** Em Java, `wait()` e `notifyAll()` só podem ser chamados de dentro de um bloco/método `synchronized` sobre o mesmo objeto — caso contrário, o programa lança `IllegalMonitorStateException`. Por que essa exigência existe? Relacione com o fato de que, em C com semáforos, `sem_wait`/`sem_post` **não** têm essa restrição.

### 8.3 Experimentos — inserindo atrasos e provocando erros

Assim como na Parte 2.3, faça cada alteração, rode, registre o resultado no relatório e desfaça antes do próximo experimento.

**E6 — Atraso dentro do monitor, com sincronização correta.**  
No arquivo `java/base/Produtor.java` (já com `Buffer` corrigido), mude:

```java
static final long ATRASO_MS = 0;
```

para `50` (também em `Consumidor.java`, se quiser).

> **Pergunta 14.** O resultado final continua correto? Note que esse atraso está **fora** do método `synchronized` (antes de chamar `buffer.inserir`). Agora mova a chamada `Thread.sleep(ATRASO_MS)` para **dentro** de `Buffer.inserir`, logo após o `while` de espera e antes de `dados[fim] = item;`. O comportamento muda? Por que um `sleep`

> **Pergunta 14.** O resultado final continua correto? Note que esse atraso está **fora** do método `synchronized` (antes de chamar `buffer.inserir`). Agora mova a chamada `Thread.sleep(ATRASO_MS)` para **dentro** de `Buffer.inserir`, logo após o `while` de espera e antes de `dados[fim] = item;`. O comportamento muda? Por que um `sleep` dentro de uma seção `synchronized` é seguro para a corretude, mas péssimo para o desempenho quando há muitas threads?

**E7 — Trocar `while` por `if` na condição de espera.**  
Em `java/solucao/Buffer.java` (ou na sua versão corrigida), troque:

```java
while (contador == capacidade) {
    wait();
}
```

por

```java
if (contador == capacidade) {
    wait();
}
```

nos dois métodos (`inserir` e `remover`). Aumente temporariamente `N_PRODUTORES`/`N_CONSUMIDORES` em `Main.java` para 4 cada, para aumentar a concorrência por `notifyAll()`.

> **Pergunta 15.** Rode 5 a 10 vezes. Você observa `buffer.contador` saindo do intervalo `[0, capacidade]` ou `ArrayIndexOutOfBoundsException`? Explique: quando `notifyAll()` acorda **várias** threads de uma vez, todas competem novamente pelo monitor, mas só uma de cada vez re-executa. Se a verificação da condição for feita com `if` (executada uma única vez, sem novo teste após acordar), o que pode uma thread fazer com base em uma condição que já não é mais

**E8 — Usar `notify()` em vez de `notifyAll()`.**  
Troque as duas chamadas `notifyAll()` por `notify()` (mantendo `while`). Configure `N_PRODUTORES = 2`, `N_CONSUMIDORES = 2` (ou mais).

> **Pergunta 16.** Rode várias vezes, inclusive com quantidades maiores de produtores/consumidores. O programa às vezes trava (não termina, sem lançar exceção)? Isso é um exemplo de **lost wakeup**: como só existe **uma** condição/fila de espera por objeto em Java (diferente de ter uma variável de condição separada para “buffer cheio” e outra para “buffer vazio”), `notify()` pode acordar uma thread do “tipo errado” (por exemplo, acordar outro produtor quando quem precisava acordar era um consumidor), deixando as demais esperando para sempre. Por que `notifyAll()` resolve isso à custa de acordar threads que vão simplesmente voltar a dormir?

**E9 — Remover `synchronized` de apenas um dos métodos.**  
Retire `synchronized` só de `remover()`, mantendo em `inserir()`.

> **Pergunta 17.** Isso garante exclusão mútua entre `inserir` e `remover`? Rode o programa e veja o resultado. Explique por que a exclusão mútua de um monitor depende de **todos** os métodos que tocam o estado compartilhado serem `synchronized` — não basta proteger “a maior parte” do código.

---

## 9. Parte 5 — Consultando a solução de referência (Java)

```bash
cd java/solucao
javac *.java
java Main
```


> **Pergunta 18.** Compare sua implementação com [`java/solucao/Buffer.java`](java/solucao/Buffer.java). Alguma diferença relevante?

---

## 10. Parte 6 — Síntese: semáforos x monitores

Preencha a tabela abaixo no seu relatório, com base no que você observou (não apenas teoria):


> **Pergunta 19 (síntese).** Com base nos experimentos E2/E3 (C) e E7/E8/E9 (Java), qual dos dois modelos você considera mais propenso a erros _de esquecimento_ (o programador esquece um passo) e qual é mais propenso a erros _de lógica_ (o programador escreve algo plausível, mas sutilmente errado)? Justifique com um exemplo concreto de cada laboratório.
> 
> **Pergunta 20 (síntese).** Semáforos são um mecanismo de baixo nível que pode implementar qualquer padrão de sincronização (inclusive um monitor). Monitores são de mais alto nível e “empacotam” o padrão mais comum (exclusão mútua + espera condicional). Dado isso, em que situação você ainda escolheria semáforos em vez de monitores, mesmo programando em uma linguagem que oferece monitores nativamente (ex: Java)?