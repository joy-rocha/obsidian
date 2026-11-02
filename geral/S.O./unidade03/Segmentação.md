# Motivação
um computador mantém varias tabelas durante a compilação

tenho que saber em que porção do meu processo o segmento está
- o código fonte não musa em tempo de execução entt vai ser num segmento que não cresce
- mas um segmento de estrutura de dados cresce, entt tem que ta num segmento de memoria que cresce pra caramba
- esses ponto na visão de um compilador

# um único espaço de endereçamento
fazer

#### **quando uma tabela atinge a outra**
- emite um aviso de "vc ta compilando um programa grande demais" isso não acontece !
- entt vc tira espaço de outras tabelas com muitas entradas livres (mas é bem dificil de fazer)

# SEGMENTOS
***características:***
- espaços de endereçamento independentes: seguimentos
- cada segmento é uma sequencia linear de endereços de 0 até um máximo
- segmentos diferentes podem ter tamanhos diferentes 
- o tamanho de um segmento pode variar duarnte a execução

# Preenchimento
o segmento pode ser totalmente preenchido mas como eles são grandes normlmente sobra segmento

um segmento pra cada tabbela 

### acesso a memória segmentada
numero de segmento e o endereço dentro do segmento é fornecido pelo programa pra ele acessar a memória

- tem um processo e um segmenteo de biblioteca e outro processo que tmbém usa a biblioteca entt eu não ŕeciso que cada segmento guarde o mesmo esndereço duas vezes so preciso de um e os dois podem acessar ela . por isso diferentes tipos de segmentos podem ter diferentes tipos de proteção

# sla esse topico
- implementação de segmentação pura (professor nem deu muita importancia ora isso)
- paginação mais segmentação
- 
