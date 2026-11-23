
***1- Quais as 4 funções do gerenciador de memoria, de exemplos***
controlar, alocar (em qual lacuna eu guardo tal processo), desalocar(como escolho o processo que desaloco neste momento) e gerenciar a troca

***2 - Partições de memória fixas ...***
1) fragmentação de 360KB (vai chegando os processos e ele encaixa na ordem das lacuna)

2) desperdicio de memoria pois quando uma fila vai ficando muito longa vai demorando a alocação enquanto tem outras partições maiores livres que podiam ser usadas

***3 - ocupação da memoria*** 
1) $1 - P^N$  = $1 - 0,6^3$  = $0784$ = $78,4\%$ de ocupação da memória
2) ...

***4 - registrador base (quer saber o segmento que o processo ta alocado)***
1) basta somar o endereço que eu boto mais o registrador base
2) pq pode invadir o espaço de endereçamento de outros processos, tem o custo de a cada acesso a amemoria tem que fazer a soma e uma comparação pra saber se estourou o registrador limite
3) simplesmente atualiza os registradores para a nova lacuna

### Gerencia de espaço livre

***5 - memoria e mapa de bits***
1) qual tamanho do mapa: tamanho da memoria/ quantidade de alocação -> $65536/4 = 16384$ Bytes , $67108864/64$ = $1049576$ Bits = $131072$ Bytes

ATENÇÃO: *transformar bits pra Bytes so dividir por 8* 

***6 - ele nn fez pq tem no slide***

***7- lacunas na gerencia de processos***
FF: 20 -> 30, 5 -> 15, 11->12, 9 -> 10
NF: 15, 6, 30, 12, 9, 22, 8 -> 15, 6, 5, 1, -, 22, 8
BF: 15, 6, 30, 12, 9, 22, 8 -> 15, 1, 30, 1, -, 2, 8
WF: 15, 6, 30, 12, 9, 22, 8 -> 6, 6, 10, 12, 9, 6, 8

### Memoria Virtual e Paginação



