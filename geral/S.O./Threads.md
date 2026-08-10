É o fluxo de execução sequencial de um processo. Podemos ter vária threads dentro de um mesmo processo. Um browser é um exemplo de uma aplicação multithread

# Vantagens do uso de threads

-  Economia de memória  (evita a criação de vários processos separados)
-  Acesso rápido (a troca entre theads é mais rápida que a troca entre processos)
-  Paralelismo num mesmo processo 
-  Eficiência

# Desvantagens do uso de threads
não há proteção entre as threads, logo elas podem mexer entre si e podem fazer qualquer coisa dentro do espaço de endereçamento.

# Criação, Término e Estados
seguem o mesmo padrão que os processos
