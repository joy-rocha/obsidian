
# PROCESSOS

#### ATENÇÃO:
**compila**:
```bash 
gcc -Wall -o lab1_1 lab1_1.c
```

  **executa o lab**:
```bash
.\labX_Y
```

---
## questão 01

ps -p 8607 -o pid,ppid,state,cmd
cat /proc/8607/
ls /proc/8607/

![[Pasted image 20260819200746.png|427]]

**▪ Q1. Qual processo é o pai do seu programa? Por quê?**
	8344, pois o PPID representa a identificação do processo pai. A coluna **PPID** significa _Parent Process ID_ (ID do Processo Pai) e indica qual processo iniciou a execução do programa.

**▪ Q2. O que significa a letra na coluna state do ps? (Consulte man ps, seção "PROCESS STATE**
**CODES".)**
	0 $S$ representa o status e o outro representa o Sleep, porque o pai ta dormindo. A coluna **S** indica o **Estado** (_State_) do processo. A letra **`S`** significa **Interruptible Sleep** (aguardando/dormindo de forma interrupível). Isso indica que o processo `./lab1_1` está pausado aguardando algum evento ou operação de entrada/saída (E/S) para voltar a executar.

**▪ Q3. O diretório /proc/PID é um diretório "de verdade" no disco? O que ele representa?**
	Não, ele não é um diretório físico gravado no **disco** (HD/SSD). Trata-se de uma interface virtual mantida na memória RAM pelo Kernel do Linux através do `procfs`. Essa abordagem é utilizada por ser significativamente mais rápida, evitar o desgaste do hardware e gerenciar com eficiência dados efêmeros (que só fazem sentido enquanto o processo está rodando).

$-$ O **procfs** (_proc filesystem_) é um sistema de arquivos virtual (ou pseudo-sistema de arquivos) usado em sistemas operacionais parecidos com o Unix, como o Linux.

---
## questão 02

**pid_t $a$ = fork();**

**visão DO PAI:**
Nessa variável $a$  é guardado o PID do último filho (quando da certo o fork), quando da errado retorna "-1" (um valor negativo)

**visão DO FILHO:**
tem um PID próprio e no mesmo $a$ armazena o retorno do fork (0 = certo; -1 = errado)

- **Total de processos ativos ao final:** $2^n$ (incluindo o processo pai original)
- **Quantidade de _novos_ processos criados:** $2^n - 1$
-  **$n$ é a quantidade de comandos fork()**

- **No Processo Pai:** O `fork()` retorna o **PID do filho** criado (um número positivo, ex: `13000`). O pai recebe esse valor para saber que o processo filho existe e poder gerenciá-lo (usando funções como `wait()`).
- **No Processo Filho:** O `fork()` retorna **`0`**. Esse valor funciona como um aviso para o novo processo indicando: _"você é o filho"_.
- **Em caso de erro:** Retorna **`-1`** apenas para o pai (nenhum filho é criado).

![[Pasted image 20260819204845.png|379]]

#### **funcionamentodo fork**
>O fork() clona o pai e roda a partir de onde o pai parou. O filho nasce exatamente na linha seguinte ao fork(), e a partir daí, pai e filho seguem seu caminho de forma independente, mas executando juntos o código (agr temos 2 processos rodando)

**▪ Q4. Por que a linha "Antes do fork" aparece uma vez, mas a "Linha final" aparece duas?**
	Poruqê  a linha é exeo fork duplica os processos assim, cada processo vai execultar os prints 

**▪ Q5. Por que o mesmo fork() "retorna dois valores diferentes"?**
	O `fork()` clona o processo e entrega um retorno para o pai e outro para o filho para que o código consiga identificar qual papel cada um deve assumir:
	- **No processo pai:** retorna o **PID do filho** ($> 0$) para que o pai possa monitorá-lo.
	- **No processo filho:** retorna **`0`** para indicar que este é o processo criado.

**▪ Q6. A ordem de execução entre pai e filho é determinística? Quem decide quem roda primeiro?**

---
## questão 03

#### memória compartilhada
>Um ponto central: após o fork(), pai e filho têm espaços de endereçamento separados. As
   variáveis têm o mesmo nome e o mesmo endereço lógico, mas são cópias independentes.

![[Pasted image 20260820104235.png|303]]

**▪ Q7. Os endereços impressos são iguais, mas os valores diferem. Como isso é possível? (Dica:**
**memória virtual.)**
pois apesar de ser processos diferentes as variáveis são cópias idênticas

**▪ Q8. Pesquise o termo copy-on-write. O Linux realmente copia toda a memória no instante do**
**fork()?**

**▪ Q9. Guarde este resultado: ele será contrastado com threads no Lab 2.3.**

---
## questão 04

#### comando EXEC()
>fork() cria um clone; para o filho executar um programa diferente, usamos uma função da família
  exec() (execl, execv, execvp, ...). O exec() substitui a imagem do processo atual pelo novo
  programa: mesmo PID, código completamente novo. Se o exec() der certo, nada depois dele
  executa.

**-> matém o mesmo PID e troca apenas o programa dentro do processo**

faz o filho executar um prograa diferente do pai,  o **execlp()** sobrescreve um codigo objeto em um determinado processo.


**▪ Q10. Por que fork() e exec() são chamadas separadas, em vez de uma única chamada "criar**
**processo rodando programa X"? Que flexibilidade esse design dá?**

**▪ Q11. O PID muda após o exec()? Verifique com getpid() antes e usando ps sobre o**
**programa executado (troque ls -l por sleep 30 para dar tempo).**

---
## questão 05

Um processo termina quando: retorna de main, chama exit(), ou é morto por um sinal. Ao
terminar, ele deixa um status de saída que o pai deve coletar com wait()/waitpid().

**KERNEL**
>O **kernel** é o núcleo do sistema operacional que controla o hardware. A **thread de kernel** é uma linha de execução gerenciada diretamente pelo próprio sistema operacional para realizar tarefas internas ou processar comandos de programas

**▪ Q12. O que acontece se o pai chamar wait() sem ter filhos vivos?**
**▪ Q13. Por que o código de saída é limitado a 0–255?**
**▪ Q14. Qual a diferença entre wait() e waitpid()?**

---
## questão 06

**ZUMBI e ÓRFÃO**
>Zumbi: o filho terminou, mas o pai ainda não chamou wait(). O kernel mantém uma entrada
  mínima na tabela de processos (só para guardar o status de saída).
  -
  Órfão: o pai terminou antes do filho. O filho é "adotado" pelo init/systemd (PID 1) ou por um
  subreaper

**▪ Q15. Um zumbi consome CPU? Consome memória? O que exatamente o kernel mantém dele?**
**▪ Q16. Por que zumbis em excesso são um problema real em servidores? Quem é o culpado: o filho ou o pai?**
**▪ Q17. É possível matar um zumbi com kill -9? Teste e explique o resultado.**

---
## questão 07
