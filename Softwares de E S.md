independência do dispositivo
nomeação uniforme

(usa a filosofia de tratar tudo que existe como um arquivo pq quase tudo faz as mesmasm oérações : escrever, ler, abrir, fechar)
- escrever programas que acessem qualquer dispositivo sem conhecelos previamente
- faz operações gerericas como: open, read, write, close

### CAMADAS DE SORFTWARE DE E/S
*uma solicitação desce pelas camada e um erro ou resposta sobe*

| processo do usuario                    |                                                                                                                                                                                                         |
| -------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| softwares independentes de dispositivo | interface uniforme para os drives (read, whrite, cloe, open), responsavel pela buferização, relato de erros alocaçã e desalocaão de dispositivos dedicado, tamanho de bloco independente do dispositivo |
| drives de dispositivos                 | código especifico de cada dispositivo que sabe como mexer no controlador do dispositivo e rodam no núcleo                                                                                               |
| tratadores de interrupção              |                                                                                                                                                                                                         |
| hardware                               |                                                                                                                                                                                                         |
o dispositivo de bloco de caracteres o SO conversa com ocontrolador, a cpu fala com o controlador por portas E/S mapeadas na memoria, ...


### CASOS-LIMITES
tem algumas operações que so alguns dispositivos possuem, comoo ejetar do pendrive

### TRATAMENTO DE ERROS

é tratado tão peto do hardware como for possível e ele vai jogando pras camadaas de software acima

**transferências síncronas** 
**transferêcia assíncrona** (hardware) avisa quando termina

### BUFERIZAÇÃO
nem sempre os dados podem ir direto ao destinno

dispositivos dedicados são usados por um usuario por vez , já os compartilhaods são usados por diverssos usuario

### TIPO DE E/S
- E/S programada - a cpu executa sozinha toda a transferencia, tbm chamada de polling
	- desvantagem a cpu espera terminar tudo pra  fazer outra coisa
- E/S orientada a interrupção - uma interrupção a cada caracter
	- desvantagem:  
- E/S usando DMA - uma unica interrupção a cada transfrerencia completa
	- desvantagem: precisa de hardware adcional

**quanto menos a CPU participa, mains preciso é o hardware