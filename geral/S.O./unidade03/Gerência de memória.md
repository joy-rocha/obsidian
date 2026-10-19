
---
# ***Gerenciador de memória***
- Controlador
- Alocar
- Desalocar (memoria dos processos quando terminam)
- Gerenciar a troca 

### Gerenciamento Básico de memória
mono-programação sem troca de processos (um programa por vez) e o S.O e o programa do usuário. Isso na época de cartões perfurados

**-> Por que alocar o S.O no começo ou no final da memória interfere no desempenho do sistema**
Porquê se o programa for ler em sequência ele vai ter que percorrer menos ou mais pra acessar e isso varia o tempo e esforço 

---

>*Multiprogramação é muitos programas usando a memória ao mesmo tempo,não necessariamente usando a CPU*

# **Multiprogramação com partições fixas**
- Muitos processos executando "simultaneamente"
- Memória dividida em N partições fixas de tamanhos diferentes
- Cada $JOB$ é  associado a menor partição de memória possível para mantê-lo 
- O espaço não usado dentro da partição é perdido

### A alocação de memória
 É feita na menor partição possível que ainda cabe o processo para evitar a fragmentação de memória, já que a memória que sobra depois que um processo é alocado numa partição se perde (não pode ser alocada enquanto tiver com um processo ativo)

### Partições fixas: filas de entrada
***2 TIPOS:***
**Múltiplas filas:** uma fila para cada partição de memória
**Fila única:** uma única fila de processos para ser distribuído nas partições ao chegar sua vez

---

# **Modelo de multiprogramação**

#### *Probabilístico:*
$$\text{utilização da CPU:  }
1 - P^N$$

$P$ - fração de tempo que um processo passa esperando E/S
$N$ - é a quantidade de processos, o **grau de multiprogramação**


---
# Relocação
==🔴fazer isso aqui==

---
# Proteção
Um processo 'mal-intencionado' pode escrever em um endereço que não lhe pertence

- **Solução**: Registrador **BASE** e **LIMITE**
- **Desvantagem**: Vai precisar de uma soma e um 'IF' a cada acesso de memória. (OCIOSO)

---
# Troca de Processos
- Mais processos do que cabe na memória
- Troca de processos inteiros entre o disco e memória: **swapping** (memória virtual de 2x o tamanho da RAM)

### Lacunas e crescimento
Uma das formas de resolver lacunas é a desfragmentação de memória

**Alocação de memória para processos de...**
$-$ memória fixa: 
$-$ memória variável: (complexidade $N$ proporcional ao tamanho do processo)

---
# Gerenciamento com mapa de BITS
```c
[P . 0 . 5] -> [L . 5 . 3]
```
P de processo, que começa no 0 e preenche 5 espaços
L de lacuna, vai do 5 e usar 3 espaços

mapa de bits é algo assim:
```
11111100000
11100011111
00011110000
```
1 = espaço ocupado
0 = espaço livre

***Desvantagens:*** 
- Vamos ter uma quantidade de bits proporcional ao tamanho da memória pra guardar as coisas e isso gasta memória
- Alocar K unidades exige procurar K sla...

==🔴MDS TEM CONTA PRA FAZER O PROFESSOR DISSE==

---

# Gerenciamento com listas encadeadas
lacunas vizinhas tornam-se uma só (se juntam) 

---
