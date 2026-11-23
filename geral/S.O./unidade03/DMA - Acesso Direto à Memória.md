
DMA é um pedaço de hardware que realiza tranferencia entre dispositivos de memoria sem passar pela CPU. E quando a DMA termina ela avisa a CPU vi uma interrupção.

quero realizar uma operração entre um dispositivo e um pedaço de memória, como uma camera de seguranca que armazenas os videos na memoria, mas para isso cada byte de imagem de frame tem que passat pela cpu para armazenar, isso gasta cpu atoa. Se o dispositivo puder se comunicar diretamente a memoria seria mais simples e menos custoso

podemos ter um DMA paracada dispositivos ou um unico controlador DMA para todos (é mais comum)

trade off - ganha numa coisa, perde na outra (tudo tem um custo)

exemplos: leitura de disco ou ssd, placa de som, microcontrolar


DMA PARA MULTIPLOS DISPOSITIVOS
deve ser genérico o bastante para transferir todos os tipos de dispositivos conectados 

- modo rajada(brust): o bloco inteiro de uma vez, mais eficiente, mas segura o barramento por mais tempo
- transferir 1 bye por vez :



