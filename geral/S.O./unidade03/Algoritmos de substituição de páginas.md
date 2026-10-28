
>retirar uma página poco referenciada e onde eu tenho cache eu tenho esse peoblema de memoria chache da CPU e cache de página em servidor web

==🔴veja as vantagen se problemas de cada opção==

# ALGORITMOS DE SUBSTITUIÇÃO:
---
# Algoritmo ótimo de substituição
não tem ele mas ele é a base quanto mais os outros se aproximar desse, melhor! ele é a utopi a meta

----
# NUR - Não Usada Recentemente
usa bits de referencia (R) e modificação (M), são flags

***estados possiveis para substituição:***
- não referenciada, não modificada : melhor vitma
- referenciada, não modificada
- não referenciada modificada
- referenciada modificada: pior vitma

_fácil de implementar mas não é o indicado pq pode jogar fora a página mais usada_

---
# FIFO e segunda chance
remove a página mais antiga e descarta 

---
# Algoritmo do relógio
é um fifo com lista duplamente encadeada e é circular

---
# LUR - menos usada recentemente
ruim pq se foi usado recenemente é bem provável que seja usado de novo entt fica meio esquisito esse descarte 

***CUSTO:***
possui custo computacional alto
a cada referencia a memoria a lista tem que ser atualizada
viável sócom hardware dedicado

# LRU com hardware: 1º modo
O hardware tem um contador C de 64 bits
 C é incrementado a cada instrução
 Cada entrada da tabela de páginas tem um campo para guardar C
 Após cada referência à memória, o valor de C é copiado para a entrada da página
 Em uma falta de página, remove a página com o menor valor de C
 
-> o problema é que eu vou ter que guardar 64bits vezes a qantidade de memória

# LRU com hardware: 2º modo
- com n molduras de página, o hardware mantém uma matriz n X n de bits, iniciada com zeros
- quando uma página K é referenciada, toda linha K recebe 1, depois toda coluna K recebe 0
- o problema é que ele aumenta a K e diminui a quantidade de referencia de todas as outras páginas, ai quando eu quiser trocaré com procurar a que tem mais zeros na linha

---
# NFU - Não Frequentemente Usada
> É um contador em software associado a cada página 

■ Problema: o NFU nunca esquece nada
■ Páginas muito usadas em um passado remoto mantêm o contador alto e não saem da memória

Solução: envelhecimento (aging). A cada tique, o contador é deslocado 1 bit para a direita e o bit R entra pela esquerda. Referências recentes pesam mais.

_o bit referenciado fica no MSB e a cada tic do relógio ele é deslocado pra direita e na medida que o tempo passa ele perde seu valor._ na tabelinha ele olha parra os bits de referência, as flags , se tiver 1 ele desloca e bota 1, já se for 0 bota zero e desloca pra direita

---
# ==🔵ANALISAR ISSO EM CASA ==

# Envelhecimento: limitação
- quantidade limitada de bits: 8 bis tipo 0000110  e 00011111 tenho isso aí com 5 tiques de relogio vai sendo deslocado e fica 00000000 e 0000000 só que uma era bem mais velha, mas acaba que uma hora elas ficam iguais e tem a m,esma chance de ser tirara

- dieferente do LRU, não destingue a ordem de referência dentro do mesmo tique
- traid off

---

# Conjunto de trabalho
paginação por demanda: as paginas são carregadas só quando referenciadas e não antecipadamente
localidade de referência:em cada fase de execução, o processo acessa uma pequenaquantidade de paginas
conjunto de trabalho: ....

# Conjunto de trabalho - pré paginação
se o conjunto de trabalho está na memória há pucas faltas de páginas

---
# wsCLOCK
COMBINA CONJUNTO DE TRABALHO COM O ALGORITMO DO RELOGIO 

#### **REGRA**:
R=1 -> pagina foi usada no tique atual e não é candidata
R=0 e idade maior que T: está fora do conjunto

>ele procura o menor valor, e a idade dela, ele ve se foi referenciada recentemente e olha a idade, se ela não foi referenciada e évelha tem uma idade antiga, eu substituo essa pagina veia, é tipo o algoritmo de segunda chance 

---


coloque a tabelinha dos slides qui ela esume todos os algoritmos daqui


