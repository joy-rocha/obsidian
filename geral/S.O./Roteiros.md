
# processos
**Q1.** Qual processo é o pai do seu programa? Por quê?
**Q2.** O que significa a letra na coluna state do ps? (Consulte man ps, seção "PROCESS STATE
CODES".)
**Q3.** O diretório /proc/PID é um diretório "de verdade" no disco? O que ele representa?

Q7. Os endereços impressos são iguais, mas os valores diferem. Como isso é possível? (Dica:
memória virtual.)
Q8. Pesquise o termo copy-on-write. O Linux realmente copia toda a memória no instante do
fork()?
Q9. Guarde este resultado: ele será contrastado com threads no Lab 2.3.

# ATENÇÃO:

**compila**
gcc -Wall -o lab1_1 lab1_1.c

**executa**
.\nome

---
## questão 01
ps -p 8607 -o pid,ppid,state,cmd
cat /proc/8607/
ls /proc/8607/
![[Pasted image 20260819200746.png|427]]

---
## questão 02

PAI -> PID PRÓPRIO
FILHO -> LÊ COMO 0 O PID_T DE VARIÁVEL AGORA
PAI -> QUE EXECUTOU FORK, NAQUELA VARIÁVEL AGORA RECEBE O PID DO ÚLTIMO FILHO

**pid_t $a$ = fork();**

**O PAI:**
Nessa variável $a$  é guardado o PID do último filho (quandoda certo o fork), quando da errado retorna "-1" (um valor negativo)

**O FILHO:**
tem um PID próprio e no mesmo $a$ armazena o retorno do fork (0 = certo; -1 = errado)

os filhos n sabem que tem pai,mas os pai sabem que tem os filhos
- **Total de processos ativos ao final:** $2^n$ (incluindo o processo pai original).
- **Quantidade de _novos_ processos criados:** $2^n - 1$.
-  $n$ é a quantidade de comandos fork().

- **No Processo Pai:** O `fork()` retorna o **PID do filho** criado (um número positivo, ex: `13000`). O pai recebe esse valor para saber que o processo filho existe e poder gerenciá-lo (usando funções como `wait()`).
- **No Processo Filho:** O `fork()` retorna **`0`**. Esse valor funciona como um aviso para o novo processo indicando: _"você é o filho"_.
- **Em caso de erro:** Retorna **`-1`** apenas para o pai (nenhum filho é criado).

![[Pasted image 20260819204845.png|379]]

Q4. Por que a linha "Antes do fork" aparece uma vez, mas a "Linha final" aparece duas?
o fork duplica os processos assim,cada processo vai execultar os prints
Q5. Por que o mesmo fork() "retorna dois valores diferentes"?
Q6. A ordem de execução entre pai e filho é determinística? Quem decide quem roda primeiro?

---
## questão 03

