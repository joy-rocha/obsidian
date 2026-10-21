# Memória Virtual
> É dividir o programa em pedaços e manter na memória principal apenas os pedaços em uso a cada momento, o restante fica no disco e so é trazido quanodo vai usar a CPU

- o conjunto de programas pode ser maior que a memória física
- suponha um programa de 16 MB em uma memória de 4 MB
- swapping é uma troca do que ta na memória física pra virtual
# Endereços Virtuais
> Endereços virtuais saem da na CPU e vai pra  MMU, e endereços físicos saem da MMU e vão para ....

### Páginas virtuais e molduras físicas
>Página é a unidade mínima de alocação de memória
  Moldura de página é a memória correspondente na memória física

-  Endereços virtuais estão mapeados na memória física 
	  [espaço virtual] -> [memória f´isica]

-  Páginas e molduras tem o mesmo tamanho sempre em potências de 2
-  Transferências de disco são sempre emunidade de página

### Page fault (falta de página)
>É uma interrupção que a MMU faz quando falta página, "vc ta tentando acessar um endereço que não ta na memória física"

***COMO ACONTECE:***
- uma trap de aviso dee page faut é enviada
- o SO pega a página menos usada
-  aloca no menos usado ....

# Traduçãode endereços
>16 bits tem um tal de ofset, explica isso meu Deus. tem que ver isso aqui

***PROBLEMAS DE TABELAS DE PÁGINA:***
- o mapeamento tem que ser rápido
- a tabela de páginas pode sermito grande
- com 32 bits de 4kbts podemos ter 2^(32) = 1 milhão

***DESEMPENHO***:
- uma instrução em linguagem de montagem pode fazer referencias a tabelas de páginas

***ONDE GUARDAR TABELAS DE PÁGINAS***
- em registradores da CPU: toda tabela é guardada no registrador pra cada processo, a vantagem é que a tradução é bem rápida mas pra troca de contexto com isso é absurdo de ruim
- na memória: um registrador guarda a penas o endereço de inicio da tabela, troca de contexto rápida, mas toda vez que acessa a memória exige m segundo acesso pra saber em que página tá

# TLB memória associativa
uma opção tipo uma memória CACHE so pra guadar isso, ou seja, um pequena memória associativa dentro da MMU que guarda as traduções página( moldura usadas recentemente). A vantagem é a tradução imediata sem consultar, mas tem a falha do TLB miss, mas isso funciona bem pq os programas acessam poucas páginas por vez

# Tabelas de páginas multiníveis
> É a paginação da paginação. A tabela de primeiro nível aponta pra tabela de segundo nível


entrada de uma tabela