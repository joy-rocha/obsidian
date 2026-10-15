
# jantar dos filósofos
- temos 4 filósofos que comem usando talhesres mas so temos 2 pares de talheres para os 4
- a solução é um semáforo em cada filósofoe quandoeu for usar o talherpra comer (acessar o recursocompartilhado) os meus vizinhos do meu lado ficam bloqueados até que eu solte os talheres
- todos os filósofos pegam o garfo da esquera vai esperar o outro soltar e acabam entrando num dead lock

> o filosofo canhoto tenta pegar o garfo da DIREITA, se não conseguir ele dorme, e se pegar ele segura até pegar o esquerdo também e poder comer.

> o filósofo com o array de estados só permite que um filosofo pegue os garfos se os seus vizinhos estiverem com os garfos liberados

> o filosofo com mutex garante que cada filoso vai ter a sua vez de comer sem que outro o interrompa

# barbaeiro sonolemnto
- se não tem niguém pra cortar cabelo barbeiro dorme
- clientes esperam na fila até ser atendido 
- se a fila encher e vc chegar nela cheia vc não é atendido (processo é descartado)

---

***LISTA DE EXERCÍCIOS***
- região criticas podem ser de recursos compartilhados diferentes
- é o trecho de código que um processo usa pra acessar o recurso compartilhado

