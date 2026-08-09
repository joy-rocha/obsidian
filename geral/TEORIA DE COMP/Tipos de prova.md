	matemática discreta

[exercícios sobre](https://youtu.be/UDc-XhQ81Dc?si=QI7WvbvPV_etf0Vi)

# DEFINIÇÕES

### **PROVA**:
	Serve para pegar determinada afirmação que seria nossa hipótese P e provár-mos ou demonstrár-mos que atraves dela podemos chegar em outra afirmação que seria a tese Q, utilizando de teoremas e axiomas matemáticos aplicados em P até chegar em Q, provando as afirmações.
	
	FORMALIZANDO: Sendo a afirmação P() uma hipótese e a afirmação Q() uma tese. Queremos fazer com que P chegue até Q de forma que podemos dizer que P $->$ (implica em) Q
	
	Uma prova é simplesmente um argumento lógico tão convincente que demonstra que um enunciado é verdadeiro em um sentido absoluto

### **ENUNCIADOS MATEMÁTICOS**:
	○ Pode expressar propriedades de objetos
	○ Pode ser verdadeiro ou falso
	○ Não pode ser ambíguo
### TEOREMAS:
	○ Enunciado matemático demonstrado como verdadeiro
	○ Enunciados de especial interesse, pois revelam alguma verdade
	que tem consequências no nosso entendimento da nossa realidade
### LEMA:
	○ Enunciado verdadeiro, útil para provar outros enunciados usados
	como teorema
	
### COROLÁRIOS:
	○ Teoremas de fácil dedução a partir de outros teoremas
	○ Consequências triviais de outros teoremas
	Direção do enunciado


■ P↔Q
■ P→Q
■ P←Q


# Prova por Contra - exemplo
Consiste em observar instâncias do enunciado e encontrar uma única instância que falhe ao ter assumido propriedades definidas. Se uma única falha for encontrada o enunciado é provado como falso.

=> **sempre é testando, vai do zero ao 10 até achar algum que prove que é falso**

# Prova por Construção 
Serve para provar que um objeto EXISTE, consiste em demonstrar explicitamente como esse objeto pode ser encintrado ou construído

# Prova por Contradição
Você assume temporariamente que o enunciado que quer provar é **falso**. A partir dessa suposição, seguimos passos lógicos até encontrar um "absurdo" (uma contradição com um fato conhecido). Como a lógica não aceita absurdos, então a minha suposição inicial (enunciado falso) está **errada**, logo o enunciao original deve ser **verdadeiro**

- finjo que é falso -> acho um absurdo -> enunciado verdadeiro

# Prova por Indução
possui dois passos:
	**Caso Base:** Provar que o enunciado vale para o primeiro elemento (geralmente n=1 ou n=0)
	**Passo Indutivo:** Você assume que vale para um número genérico k (**hipótese indutiva**) e usa isso para provar que, obrigatoriamente, terá que valer para o próximo número (k+1)

entt o passo a passo sempreéfazer o caso base com 1 e dps substituir a icógnita por K e resolver e dps subtituir por (k+1), se o k e o k+1 satisfazer o que se diz no enunciado a prova deu verdae. é isso ?

**Formal:** O caso base deve ser o **menor valor** para o qual a afirmação afirma ser verdadeira. Se o teorema diz "para todo n≥0", seu caso base será n=0. Se disser "para todo n≥2", seu caso base será n=2

$A² x A³ = A⁶$

**primo**: série infinita de números maiores que 1 que só são divisíveis por um e por ele mesmo
**racional**: a/b tal que a e b $\in$ aos $\mathbb{Z}$ e b $\neq$ 0
**número par**: é um número inteiro que pode ser escrito na forma 2k, onde k é um número inteiro qualquer
**_número ímpar:** pode ser escrito na forma_ 2k+1_, onde_ k _é um inteiro

**CASO BASE**
**analisamos os dois ladoscom o mesmo K se der igual ele satisfaz**
ara verificar o caso base da afirmação $1 + 3 + 5 + \dots + (2n-1) = n^2$ para $n = 1$:

- **Lado esquerdo (Soma):** O primeiro termo da soma é $1$ (que equivale a $2(1) - 1 = 1$).    
- **Lado direito (Fórmula):** Substituindo $n = 1$ em $n^2$, temos $1^2 = 1$.

Como ambos os lados resultam em $1$, temos que $\text{Lado Esquerdo} = \text{Lado Direito}$ ($1 = 1$).
Portanto, **sim**, seu caso base está correto!

**ATENÇÃO**
- Note que os primeiros termos dessa nova soma correspondem exatamente à nossa **Hipótese de Indução**:
    
    $$\underbrace{1 + 3 + 5 + \dots + (2k - 1)}_{= k^2} + (2(k+1) - 1)$$
    
- Substituímos esse bloco inicial por $k^2$:
    $$k^2 + (2(k+1) - 1)$$

Quando o problema afirma que $\text{Soma}(n) = \text{Fórmula}(n)$, você assume como regra que a fórmula vale para $n = k$

***ai so simplificar e ver se o resultado da igual a hipotese so que +1***

**NUMERO PAR**
todo número par podeser escrito como $2k$ 

**NÚMERO ÍMPAR**
$2k+1$

**DIVISÍVEL** 
Quando dizemos que um número $X$ é divisível por um número $d$, significa que $X$ é um **múltiplo** de $d$. Matematicamente, isso se traduz para:

$$X = d \cdot m \quad (\text{onde } m \text{ é um inteiro qualquer})$$

**Exemplos para guardar o padrão:**

- **Divisível por 3:** escrevemos $3m$
    
- **Divisível por 5:** escrevemos $5m$
    
- **Divisível por $k$:** escrevemos $k \cdot m$

**POTÊNCIAS**
$a^{x+y} = a^x \cdot a^y$


A soma e a divisão de racionais geram outro racional


### 10 Questões Regulares

1. **[Construção]** Prove que a soma de dois números ímpares é sempre um número par.
* *Dica:* Lembre-se da definição algébrica de um número ímpar (ex: $2k + 1$) e faça a soma de dois ímpares distintos.
* $2A+1+2B+1 = 2A+2B+1 = 2(A+B+1), considerando (A+B+1) = k, temos -> 2k (par)$
* logo o enunciade é tido como FALSO 


2. **[Contraexemplo]** Refute a seguinte afirmação: "Para todo número primo $p$, $p$ é um número ímpar."
-  n = 1, primo, ímpar
-  n = 2, primo PAR , logo o enunciado é FALSO
  
3. **[Contradição]** Prove que não existe um número racional $x$ tal que $x^2 = 2$.
* *Dica:* Assuma que a afirmação é verdadeira e escreva $x$ como uma fração irredutível $\frac{a}{b}$. Em seguida, mostre que isso leva a um absurdo onde tanto $a$ quanto $b$ devem ser pares.

$$2k^2 = b^2$$

**O Absurdo (A Contradição):** Se $a$ é par e $b$ é par, significa que **ambos são divisíveis por 2**. Mas nós começamos a prova afirmando categoricamente que a fração $\frac{a}{b}$ não podia ser simplificada por número nenhum!

4. **[Indução Matemática]** Prove que a soma dos primeiros $n$ números naturais segue a fórmula:

$$1 + 2 + 3 + \dots + n = \frac{n(n+1)}{2}, \forall n \ge 1$$


5. **[Construção]** Prove que, se $a$ e $b$ são números racionais tais que $a < b$, então sempre existe um número racional $c$ estritamente entre eles (ou seja, $a < c < b$).
6. **[Indução Matemática]** Prove que a expressão $4^n - 1$ é divisível por 3 para qualquer número inteiro $n \ge 1$.
* *Dica:* Utilize a mesma estratégia algébrica de evidência que usamos para provar a divisibilidade por 5.


5. **[Contraexemplo]** Refute a afirmação: "O polinômio gerador $n^2 + n + 41$ resulta em um número primo para absolutamente todos os números naturais $n \ge 0$."

6. **[Contradição]** Prove que não existe "o maior número inteiro par".
* *Dica:* Suponha por absurdo que existe um maior número par $M$. O que acontece se você somar 2 a ele?


9. **[Indução Matemática]** Prove que $2^n > n^2$ para todos os números inteiros $n \ge 5$.
* *Dica:* O caso base aqui não será o 1, preste atenção à inequação do enunciado.


10. **[Construção]** Prove que o produto de dois inteiros pares consecutivos é sempre divisível por 8.

---

### 3 Questões Extras (Nível Desafio)

Estas questões exigem uma manipulação algébrica mais avançada ou uma percepção lógica fora da caixa.

11. **[Construção]** Prove que, para qualquer número natural arbitrário $n \ge 2$, é possível construir uma sequência exata de $n$ números inteiros consecutivos que sejam inteiramente compostos (ou seja, nenhum deles é primo).
* *Dica de Ouro:* Pense na definição de fatorial. Avalie os termos da sequência que começa em $(n+1)! + 2$ e termina em $(n+1)! + (n+1)$.


12. **[Contradição]** Prove que existe uma infinidade de números primos.
* *Dica de Ouro:* Este é um teorema clássico de Euclides. Suponha que existe um número finito de primos, multiplique todos eles e some 1. Esse novo número gerado causará uma pane na sua hipótese.


13. **[Indução Matemática]** Prove que a soma dos $n$ primeiros termos da sequência de Fibonacci obedece rigorosamente à seguinte regra:

$$F_1 + F_2 + \dots + F_n = F_{n+2} - 1, \forall n \ge 1$$



*(Considere os primeiros termos da sequência como $F1 = 1$, $F_2 = 1$, $F_3 = 2$, $F_4 = 3$, e a regra geral de que $F_{k+1} = F_k + F_{k-1}$).*