IPC - Interprocess Comunicat ...

# Conceitos principais:

### Condição de corrida (Race Condition)
>

### Recurso compartilhado
> É qualquer coisa que pode ser compartilhada ao mesmo tempo. ex: memória, arquivos...

### Exclusão Mútua
> Dois processos NÃOpodem usar o recurso compartilhado ao mesmo tempo. A exclusão evita a condição de corrida

### Região Cŕitica
>  É a área de um programa que ta acessando a memória compartilhada

### Exclusão Mútua com Espera Ociosa
> <u> Desabilitamos </u> as interrupções quando um processo entra em uma região crítica e só reabilita quando o processo termina

==**OBS**==: é impossível prever quando a fatia de tempo vai acabar, por isso é muito difícil reproduzir um erro


# 4 Critérios para *GARANTIR* a Exclusão Mútua

-  Nunca 2 processos podem estar em região crítica simultaneamente
-  Deve funcionar independente da CPU ou memória
-  Nenhum processo deve esperar eternamente para ser executado
-  Nenhum processo que está em regiçao crítica pode bloquear outro processo


# Variável de trava
sobrescrição de variável, logo, dois processos em região crítica

# Test and Set Lock -> TSL
tem espera ociosa e perde tempo (beat wait) , MAS resolve a exclusão mútua e tem o auxílio do hardware.

==termos:==
 - **Dead Lock**: um processo entra em loop esperando outro
 -  **Buffer**: é uma proteção contra a variação da taxa de dados
 -  **Starvation**: Um processo segura o recurso e outro nunca consegue usar

# Setup and Wake-up
> Que significa dormir e acordar, eles botam um processo pra dormir pra os dois não acessar a região crítica ao mesmo tempo, aí quando um sair de lá vocẽ pode acordar o outro

# Producer and Consumer
> Temos um buffer, um cara que produz se ouver espaço e um que consome se tiver itens

==SUGESTÃO DE LEITURA==
pág 82-89






