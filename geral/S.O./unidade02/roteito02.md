https://app.notion.com/p/Desafio-de-Depura-o-Bugs-de-Sincroniza-o-c6ace8e50e47824da12581fdb203fdfc
---

# Desafio de Depuração — Bugs de Sincronização

Revisão pré-prova sobre comunicação interprocessos, semáforos (C) e monitores (Java).

## Como funciona

Há **11 programas prontos para compilar e rodar [nesse repositório](https://github.com/henriquecunha2/desafio-sincronizacao)** (5 em C, na pasta [`c/`](c/), e 6 em Java, na pasta [`java/`](java/)). Cada um tem um bug de sincronização diferente (ou, em um caso, nenhum bug — parte do exercício é também saber reconhecer código correto). Para cada programa, siga estes três passos:

1. **Diagnosticar.** Leia o código antes de rodar. Qual problema você espera que aconteça? Em qual linha? Qual categoria da tabela abaixo se aplica?
2. **Forçar e observar.** Compile e rode o programa (cada arquivo tem, no comentário do topo, o comando de compilação e dicas de como tornar o bug mais fácil de reproduzir — mais threads, mais iterações, rodar várias vezes seguidas). Confirme que o problema realmente acontece e anote a evidência (saída no terminal, mensagem de erro, o programa travou, etc.).
3. **Corrigir e validar.** Edite o próprio arquivo (ou copie para um novo, ex.: `trecho1_contador_corrigido.c`) aplicando a correção que você acha certa. Recompile e rode de novo — inclusive várias vezes, para os bugs probabilísticos — para confirmar que o problema não ocorre mais.

### Categorias possíveis

|Categoria|Ideia central|
|---|---|
|**A — Condição de corrida (atomicidade)**|Duas threads leem/alteram a mesma variável sem exclusão mútua; uma operação que parece uma instrução só na verdade é ler-modificar-escrever.|
|**B — Condição de corrida (visibilidade)**|Existe exclusão mútua em parte do código, mas outra parte lê o dado compartilhado sem nenhuma sincronização.|
|**C — Deadlock**|Duas (ou mais) threads ficam bloqueadas esperando uma pela outra para sempre.|
|**D — Trava vazada (lock vazado)**|Um lock/semáforo é adquirido mas existe um caminho de execução em que ele nunca é liberado.|
|**E — Lost wakeup / sinalização perdida**|Uma thread fica esperando por um sinal que nunca chega (ou chega para a thread errada), mesmo a condição já sendo satisfeita.|
|**F — Inicialização incorreta**|O estado inicial de um semáforo/variável está errado para o que o código pretende fazer.|
|**G — Uso incorreto da API de sincronização**|Uma chamada de sincronização é feita em um contexto em que não é válida (ex.: fora da região protegida).|
|**✓ — Correto**|O programa não tem bug de sincronização.|

### Compilando e rodando

**C** (dentro da pasta `c/`):

```bash
gcc -Wall -Wextra -pthread trechoN_nome.c -o trechoN
./trechoN
```

ou `make` para compilar todos de uma vez (veja o `Makefile`).

**Java** (dentro da pasta `java/`):

```bash
javac TrechoN_Nome.java
java TrechoN_Nome
```

Vários bugs abaixo são **probabilísticos** (podem não aparecer em toda execução). Um truque útil é rodar o mesmo programa várias vezes em sequência:

```bash
for i in $(seq 1 10); do ./trecho1; done
```

Alguns programas em C têm um **watchdog** embutido (um alarme de alguns segundos): se o programa travar por causa de um deadlock, uma mensagem `*** POSSIVEL DEADLOCK ***` aparece sozinha e o processo é encerrado — você não precisa apertar Ctrl+C.

---

## Trecho 1 (C) — `c/trecho1_contador.c`

Várias threads incrementam um contador compartilhado sem nenhuma sincronização.

**Pergunta:** qual o valor esperado ao final? O programa imprime esse valor? Rode algumas vezes — o resultado muda entre execuções?

***==resposta==: é esperado que sejam feitas 800000 interações. Não, apenas 310985. O valor varia um pouco, mas mantém a proporção de incrementos perdidos.***

***==problema==: as threads alteram desordenadamente a variável contadora devido a falta de atomicidade ente a execução de sua tarefa e o incremento na variável, violando a exclusão mútua. problema de CATEGORIA A==***

---

## Trecho 2 (C) — `c/trecho2_deadlock_locks.c`

Duas threads pegam dois mutexes (`A` e `B`) em ordens diferentes.

**Pergunta:** o programa termina sozinho ou o watchdog dispara? Depois de corrigir, explique com suas palavras por que sua correção elimina a possibilidade de deadlock (não só “nesta execução”).

***==resposta:== o watchdog é disparado. A solução seria impor uma ordem estrita de aquisição (`A` depois `B`) em todas as threads, elimina-se a dependência circular necessária para a ocorrência de deadlock.***

***==problema:== a thread1 não pega as duas chaves ao mesmo tempo ent a thread2 pode pegar uma das chaves aí as duas ficam tentando pegar a outra chave esperando infinito uma pela outra. problema de CATEGORIA C*** 

---

## Trecho 3 (C) — `c/trecho3_mutex_vazado.c`

Uma função processa um “pedido” dentro de uma seção crítica e tem um caminho de erro que retorna cedo.

**Pergunta:** o que acontece com o segundo pedido, que é válido? Por quê?

resposta:

***==problema:== quando o item não ta disponível (qtd <= 0) ele da um return pra falar que n ta disponível mas esquece de liberar o mutex que abriu, aí quando outros processos ou theads tentar usar vai entrar em deadlock esperando alguém liberar. problema de CATEGORIA D***

---

## Trecho 4 (C) — `c/trecho4_semaforo_init.c`

Um semáforo usado como mutex é inicializado antes do `main`.

**Pergunta:** o programa chega a imprimir a segunda mensagem? Rode e confirme antes de olhar o código de novo.

***==resposta:== Não, ela só foi impressa após a correção***

***==problema:== o mutex é inicializado vazio e logo depois tentam usar ele, o que da erro pq n tem uso se ta vazio. problema de CATEGORIA F***

---

## Trecho 5 (C) — `c/trecho5_produtor_consumidor.c`

Uma versão do produtor-consumidor com 3 semáforos (`mutex`, `vazio`, `cheio`).

**Pergunta:** o produtor consegue inserir itens? O consumidor consegue retirar algum? Onde exatamente a comunicação entre as duas threads quebra?

***==resposta:== não pois o buffer ta cheio. Sim. Na linha 43 quando a troca do comanto cheio por vazio acontece***

***==problema:== é que enchemos o buffer mas esquecemos de avisar aí quando o mutex tenta colocar mais uma, o buffer  cheio  da erro, pq trocamos o cheio por vazio na linha 42. problema de CATEGORIA E***

---

## Trecho 6 (Java) — `java/Trecho6Contador.java`

Equivalente Java do Trecho 1.

**Pergunta:** compare o resultado com o do Trecho 1 em C — o mesmo tipo de problema aparece, mesmo sendo uma linguagem diferente?

***==resposta:== sim***

***==problema:== a falta de atomicidade das operações, problema de CATEGORIA A***

**==synchronized é o mutex do java==**

---

## Trecho 7 (Java) — `java/Trecho7Visibilidade.java`

Um método de escrita é `synchronized`, mas o de leitura não é.

**Pergunta:** leia com atenção o comentário no topo do arquivo antes de rodar — este é o bug mais difícil de forçar de forma confiável. O que aconteceu no seu computador? Mesmo que o programa termine “sem bug aparente”, isso significa que o código está correto? Por quê?

* AVISO IMPORTANTE: bugs de visibilidade sao os mais dificeis de forcar de forma confiavel -- dependem de JIT, hardware, e de o que mais o programa faz (chamadas de I/O como System.out.println() costumam criar barreiras de memoria "de brinde" que escondem o bug).

***==problema:== Uma thread escreve com proteção (`synchronized`), mas a thread que lê não usa sincronização nem `volatile`. Ou seja falta de visibilidade,problema de CATEGORIA B***

---

## Trecho 8 (Java) — `java/Trecho8LostWakeup.java`

Um buffer circular usa `if` em vez de `while` para testar a condição de espera antes do `wait()`.

**Pergunta:** que tipo de erro aparece no console (se aparecer)? Relacione com o papel do `notifyAll()` acordando várias threads de uma vez.

==resposta:== o consumidor olha se o buffer ta cheio se não tiver ele dorme e o produtor entra em cena, produtor faz 1 aí volta pro consumidore o buffer continua não cheio entt ele dorme e o produtor produz, isso té o buffer encher e o notfyall acordar as threads que dormiam e ela poderem consumir agr

==problema:== a verificação se o bufferta cheio tem que ser constante (while) pra manter os processos dormindo até que o buffer esteja cheio e pronto pra ser consumido

---

## Trecho 9 (Java) — `java/Trecho9DeadlockTransferencia.java`

Uma função de transferência entre contas usa dois `synchronized` aninhados.

**Pergunta:** por que a ordem dos parâmetros nas duas chamadas concorrentes é o que causa o problema aqui, e não a lógica de `transferir` em si?

***==resposta: ==Sim, exatamente***

***==problema:==  é a ordem, porque as threads trancam antes da outra pegar ai ficam esperando uma pela outra entrando em um deadlock. problema de CATEGORIA C***

---

## Trecho 10 (Java) — `java/Trecho10WaitForaSync.java`

Uma fila chama `wait()` em um objeto fora de um bloco `synchronized` sobre esse mesmo objeto.

**Pergunta:** que exceção o programa lança? Em que linha exatamente?

==resposta: ==

***==problema:== ***Chamada de `lock.wait()` realizada fora de um bloco `synchronized(lock)`. Em Java, é obrigatório possuir a trava do monitor do objeto para chamar `wait()` ou `notify()`, caso contrário a JVM lança a exceção `IllegalMonitorStateException`.** ***CATEGORIA F — Configuração / Uso Incorreto do Monitor** (falha na posse do lock ao invocar primitivas de sincronização).***

---

## Trecho 11 (Java) — `java/Trecho11Correto.java`

Mesma carga de threads do Trecho 8, mas com a espera condicional implementada corretamente.

**Pergunta:** rode várias vezes. Ele nunca falha? O que isso mostra em contraste com o Trecho 8, já que os dois programas são praticamente idênticos?

***==resposta:== categoria CERTOOOOO***

***==problema: ==NEHUM***

---
## Para discutir depois
- Dos 11 programas, quantos você classificou como cada categoria? Alguma categoria você não usou nenhuma vez — por quê?
- Para os trechos com deadlock (2 e 9), você consegue desenhar o grafo de espera (quem espera por quem)?
- Depois de corrigir cada programa, ele ficou mais lento, mais rápido, ou não fez diferença perceptível? Isso é esperado?
- Qual bug foi mais fácil de forçar a acontecer, e qual foi mais difícil? Por quê?

---


  

### Guia Mnemônico para a Prova (Regra de Ouro por Padrão de Bug)

Para você bater o olho no código na hora da prova e identificar a categoria rapidamente, use este mapa de diagnóstico:

  

**1. Categoria A — Condição de Corrida (Falta de Atomicidade)**

  

  

- **Sintoma na prova:** Operações como `contador++` ou leituras/escritas em variáveis globais executadas por várias threads sem proteção, gerando resultados menores que o esperado e imprevisíveis.
    
      
    
- **Gatilho de identificação:** Variável compartilhada sendo alterada sem `mutex`, sem `synchronized` e sem tipo `Atomic`.
    
      
    
- **Como resolver:** Envolver o trecho num `mutex_lock/unlock`, num bloco `synchronized`, ou usar classes atômicas.
    
      
    

**2. Categoria C — Deadlock (Espera Circular)**

  

  

- **Sintoma na prova:** O programa começa a rodar, imprime os primeiros logs e **congela para sempre** (ou até o _watchdog_ matar).
    
      
    
- **Gatilho de identificação:** Thread 1 pega A e pede B; Thread 2 pega B e pede A.
    
      
    
- **Como resolver:** Impor uma **ordem global de locks** (todas as threads pedem sempre A primeiro e depois B).
    
      
    

**3. Categoria D — Trava Vazada (Mutex Leak)**

  

  

- **Sintoma na prova:** Funciona na primeira chamada, mas congela na segunda tentativa de usar a função.
    
      
    
- **Gatilho de identificação:** Um `if` de validação de erro ou um `return` precoce dentro da função que acontece **depois** do `lock`, mas **antes** do `unlock`.
    
      
    
- **Como resolver:** Garantir que **todos** os caminhos de saída (`return`, exceções, `goto`) executem o `unlock` ou `sem_post` antes de sair.
    
      
    

**4. Categoria F — Inicialização Incorreta**

  

  

- **Sintoma na prova:** Trava logo na **primeira** tentativa de dar `wait`, antes mesmo de existir concorrência ou disputa.
    
      
    
- **Gatilho de identificação:** Semáforo usado como trava de exclusão mútua inicializado com valor `0` em vez de `1` (`sem_init(&mutex, 0, 0)`).
    
      
    
- **Como resolver:** Se for trava (mutex), inicialize com **1** (porta aberta). Se for sinalização de evento, aí sim começa com **0**.
    
      
    

**5. Categoria E — Sinalização Perdida (Lost Wakeup)**

  

  

- **Sintoma na prova:** Uma thread fica dormindo no `wait` / `sem_wait` esperando um aviso que nunca chega.
    
      
    
- **Gatilho de identificação:** O produtor/notificador altera o estado (ex: insere um item no buffer), mas faz o sinal no semáforo errado (ex: chama `sem_post(&vazio)` em vez de `sem_post(&cheio)`) ou esquece de notificar.
    
      
    
- **Como resolver:** Garantir que o sinal de liberação (`post`/`notify`) corresponda exatamente ao semáforo ou condição em que a outra thread está dormindo (`wait`).
    
      
    

Guardando esses 5 cenários, você consegue matar qualquer questão de diagnóstico de concorrência.

  

Quer aproveitar que organizamos esse resumo e partir para a análise do **Trecho 7 (`Trecho7Deadlock.java`)**?