
# CONJUNTOS
uma coleção de objetos representada como unidade única que pode conter outros conjuntos

**CARACTERÍSTICAS**
- Os objetos do conjunto são chamados de membros ou elementos
- Não há repetição de objetos num mesmo conjunto
- Não há relação de ordem entre os elementos

**MULTICONJUNTO**
é um conjunto onde a repetição de elementos é considerada

**PERTINÊNCIA
- **Pertinência (∈):** 14∈C significa que o elemento 14 pertence ao conjunto C.
- **Não Pertinência (∈/): 10 ∈/ C significa que o elemento 10 não pertence ao conjunto C
- **Subconjunto (⊆)**:Um conjunto A é subconjunto de B se todo membro de A também for membro de B (diz-se que A está contido em B).
- **Subconjunto Próprio (⊊):** A é um subconjunto próprio de B se A for subconjunto de B, mas A não for igual a B.
- **Conjunto das Partes (P(A)):** É o conjunto que contém todos os subconjuntos possíveis de um conjunto A. Por exemplo, se A={0,1}, o conjunto das partes de A é {∅,{0},{1},{0,1}}.

**CARDINALIDADE**
- conjunto VAZIO: ∅ ou {}
- conjunto FINITO: {14,3,4,42}
- contunto INFINITO: {1,2,3,…}

**OPERAÇÕES COM CONJUNTOS**
   A = {2, 4}       B={2, 4, 5, 6}
   
união -> A $\cup$ B = {2, 4, 5, 6}
interseção ->  A $\cap$ B = {2, 5}

**PRODUTO CARTESIANO**
AXB é o conjunto de todos os pares ordenados (ou duplas) onde o primeiro elemento pertence a A e o segundo a B.
- **antenção**: se temos o conjunto $A$ com $x$ elementos e o conjunto **$B$** com **$y$** a quantidade de pares que o seu produto cartesiano terá é $X*Y$


 - 2 ∈ A
 - 90 ∉ B
 -  A ⊆ B : todo membro de A também é de B
 - A é subconjunto, mas não é igual a B


---

# FUNÇÕES E RELAÇÕES

**FUNÇÃO ou MAPEAMENTO** 
Objeto que estabelece uma relação de entranda e saída, ou seja, relação entre dois conjuntos e que a mesma entrada sempre produz a mesma síada

**ESTRUTURA**
- **DOMÍNIO:** conjunt de todas as entradas
- **IMAGEM:** saídas, os resultados
- **CONTRA DOMÍNIO:** conjunto de saídas possíveis
	Notação: Escrevemos f:D→C para dizer que a função f tem domínio D e contradomínio C

**ARIDADE E ARGUMENTOS**
ARIDADE ($K$) é a quantidade de argumentos que a função recebe e ARGUMENTOS são os valores que são passados para a função.
-  Unária ($K$ = 1): recebe 1 argumento
-  Binária ($K$ = 2): recebe 2 argumentos, tipo a função de soma add(2,3) = 5 [2+3=5]

**NOTAÇẼS**
- **Infixa:** O operador fica entre os valores (ex: a+b)
- **Prefixa:** O operador vem antes (ex: +(a,b))
- **Posfixa:** O operador vem depois (ex: (a,b)+)

**PREDICADO**
Um **predicado** é um tipo especial de função muito importante para a lógica computacional.

- O seu contradomínio é sempre **{VERDADEIRO, FALSO}**.
- **Exemplo:** Uma função que verifica se um número é primo. Se você der o número 7, ela devolve VERDADEIRO; se der o número 4, devolve FALSO.
- Outro exemplo das fontes é o jogo **Pedra, Papel e Tesoura**, onde a função "bate" analisa dois elementos e diz se o primeiro vence o segundo (Ex: _bate(Papel, Pedra)_ = VERDADEIRO).

https://youtu.be/ZgIwdHJhd5s?si=63aDYpxc49YOA3qR
dominio e imagem de função

---

# **CADEIAS E LINGUAGENS**

-  **Alfabeto** é qualquer conjunto finito NÃO vazio
-  **Símbolos** são os elementos do alfabeto
-  **Cadeias** é uma seuquência de símbolos de um alfabeto, escritos sem separação e podendo haver repetição (isso rpresenta uma cadeia sobre um alfabeto)

-> Ou seja, digamos que tebho esse alfabeto **&={a, c , m, e, s}**
logo, conseguimos as seguintes cadeias: **cama, mesa,  massa**

**SEQUÊNCIAS**:
Conjunto de elementos onde a ordem importa e pode haver repetição de elementos

**SUBCADEIA**:
É quando temos ums sequência de símbolos numa cadeia que aparecem consecutivamente dentro de outra cadeia.
- EX: "ria" é subcadeia de "engenharia", "papelaria","marcenaria"

**REVERSO**
é uma cadeia escrita com a ordem dos seus símbolos de tras para frente
- EX: "amor" -> "roma"

**ORDENAÇÃO LEXICOGRÁFICA**
É a mesma que a ordenação familiar do dicionário, mas com uma regra extra: cadeias mais curtas sempre vêm antes das mais longas



---


# GRAFOS

São compostos por um conjunto de pontos chamados **VÉRTICES** e linhas chamadas de **ARESTAS** que conectam estes pontos para mostrar que existe uma relação.

**DIRECIONADOS** : as arestas possuem sentido e direção
**NÃO DIRECIONADOS** : as arestas possuem uma relação mútua, não possuem sentido

**GRAU** : representa a quantidade de arestas que incidem sobre um vétice
**PESOS** : representa o custo ou distância em uma aresta

**CONEXO** : existe um caminho entre quaisquer dois vértices
**DESCONEXO** : possue partes isoladas

**SIMPLES** : não ha laços nem paralelas
**SUBGRAFO** : pedaço ou parte de um grafo maior
**CICLO** : caminho que começa e termina no mesmo vértice
**ÁRVORE** : grafo SIMPLES e CONEXO

**P C T**
-  **PERCURSO** : sequência de vértices e arestas
-  **TRILHA** : percurso, mas sem repetir arestas
-  **CAMINHO** : thilha, mas sem repetir arestas nem vertices

**ATENÇÃO:**
a soma dos graus **deve ser sempre um número par**, porque cada aresta conta como "2" na soma total (uma ponta em cada nó). Quando a soma dos graus  resulta em **ímpar**, o grafo é **impossível** de ser construído