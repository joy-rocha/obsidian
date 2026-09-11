É basicamente tornar uma instrução indivisível e atômica, porque se as instruções forem separadas, a fatia de tempo pode acabar entre elas.

# Operaçoes Indivisíveis
#### Down: 
> verifica o semáforo <u>E</u> dorme caso seja 0 (zero) . Ele meio que subtrai 1
#### Up:
> incrementa o semáforo, ou seja, adiciona um sinal de acordar . Ele meio que soma 1

---
# 3  Tipos de Semáforos

#### MUTEX:
> Garante exclusão mútua, iniciado com 1 . Veja o ex

| if(mutex == 1){<br>	cont ++    <br>} | down(&mutex) | a vantagem é que torna a instrução atômica |
| ------------------------------------ | ------------ | ------------------------------------------ |
| 2 instruções                         | 1 instrução  |                                            |

#### FULL:
> Número de processos preenchidas, iniciado com zero

#### EMPITY:
> ...

