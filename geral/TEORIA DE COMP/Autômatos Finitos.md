
***modelo:*** é uma representação da realidade na qual exclui características para focar no que interessa

**MODELO MAIS SIMPLES**
Autômato finito ou máquina de estados finita: modelo muito últil, mas com uma memória que tende a 
zero.

# Máquina de Estados
![[Pasted image 20260812131233.png|379]]
** *quando paramos em um estado que NÃO é de aceitação, ele é REJEITADO**

#### DEFINIÇÃO FORMAL
um autômato ele é um 5-upla ($Q$, $F$, q$0$, $\Sigma$) onde:

>$Q$ : é o conjunto finito de estados
  $F$ 'CONTIDO OU IGUAL'  $Q$ : é o conjunto de estados de aceitação 
  q$0\in$ $Q$ : é o estado inicial 
  $\Sigma$ : Alfabeto (conjunto finito de símbolos)
  ***letra grega lá !*** : $QX\Sigma->Q$ é a função de transição 


---

***FUNÇÃO DE TRANSIÇÃO $\delta$***
#### $\delta$ $(EstadoAtual, Entrada) = EstadoFutoro$

**EX:** $\delta$($q1,0) =q2$ 
Isso quer dizer que: eu estou no $q1$ e ele vai receber um $0$ (zero), e em seguida vai para oestado $q2$

|          | ***0*** | ***1*** |
| -------- | ------- | ------- |
| ***q1*** | q1      | q2      |
| ***q2*** | q3      | q2      |
| ***q3*** | q2      | q2      |
tabelinha de ***função de transição***

---

#### LINGUAGEM
 - Se $A$ é o conjunto das cadeias aceitas por $M$, então dizemos que $A$ é a *linguagem* da máquina $M$ e escrevemos $L(M)=A$ 
 - $M$ reconhece/aceita $A$
 - Uma máqina aceita várias cadeias, mas *apenas uma linguagem*  
 - Se o autômato não tiver nenhum estado de aceitação, dizemos que ele tem uma ***linguagem vazia***
#### COMPLEMENTO
Tudo que **NÃO É** estado de aceitação vira um, e tudo que **É** passa a ser um estado normal

**ATENÇÃO:**
$C = \emptyset$ conjunto vazio
$K =$ {**$\varepsilon$**} conjunto com cadeia vazia

SEMPRE QUE O MEU ESTADO INICIAL É UM ESTADO DE ACEITAÇÃO ELE ACEITA VAZIO COMO UM DOS ELEMENTOS DE $F$

