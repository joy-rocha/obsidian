Escalonamento é exatamente essa ação: a tomada de decisão contínua sobre qual processo ganha o direito de usar a CPU em um determinado momento. E como diferentes sistemas têm necessidades diferentes, o escalonador pode ser configurado para usar vários algoritmos.

o que é um escalonador , é um software responsável pelo gerenciamento de ordem de processamento de processos e threads de um sistema operacional

**multiprogramação** é mais de um programa rodando na memoria principal ao mesmo tempo, entõ os proceesos ficam competindo entre si pela cpu (**dois ou mais processos no estado de pronto competindo pela cpu ao mesmo tempo**)

# Tipos de espera
durante o processamento dos dados de um s.o é comum haver espera por processos, quando a cpu termina todos os processos e não tem nehum pra ela executar no momento ela fica numa espera ocupada, fica perguntando toda hora se não há processos pra rodar, isso esquenta, e exige muito do hardware pois o escalonador fica desesperado para enviar alguém para o uso da cpu. Por isso, também temos a espera ociosa, que se trata da criação de um processo falso com baixa prioridade pelo s.o, só pra a cpu ter o que rodar e não ficar louca, com isso assim que um processo real chega como o nosso falso n tem prioridade ele é interrompido e o real é processado 

### 2. O Alto Custo da Alternância (Troca de Contexto)

Quando o escalonador decide tirar o Processo A da CPU e colocar o Processo B, isso não é mágico nem instantâneo. O Sistema Operacional precisa:

1. Parar o Processo A.
    
2. Salvar onde ele parou (quais dados estavam nos registradores da CPU).
    
3. Salvar o mapa de memória dele.
    
4. Carregar as informações salvas do Processo B.
    
5. Limpar a memória cache (o que deixa o computador um pouco mais lento logo após a troca).
    

Tudo isso custa tempo de processamento. Esse tempo gasto fazendo a troca é chamado de **Overhead**. Durante uma troca de contexto, a CPU está trabalhando, mas **nenhum trabalho útil do usuário está sendo feito** (ela está apenas fazendo trabalho administrativo do próprio sistema).

# **Bursts** "surtos"
A palavra "surto" vem do inglês _burst_, que nesse contexto significa uma "explosão de atividade" ou um "período de trabalho focado". Os surtos são a execução real do programa, o momento em que o trabalho útil está sendo feito.

- **CPU Burst** = Surto de CPU (o momento em que o processador está focado rodando os cálculos matemáticos ou lógicos).
- **I/O Burst** = Surto de E/S (o momento de Entrada/Saída, esperando o disco, rede, teclado, etc.).

# Overhead
O "desperdício" de tempo (o _overhead_) é o processo burocrático de fazer a **Troca de Contexto** quando um programa muda de um surto para outro.

**O que causa o Overhead**
- **Troca de contexto (_context switch_):** O tempo que a CPU gasta salvando o estado de um processo e carregando o de outro.
- **Gerenciamento de interrupções:** O processamento que o sistema faz ao receber sinais de dispositivos de hardware.
- **Organização de memória:** O controle que o S.O. precisa fazer para gerenciar o espaço de trabalho dos programas.

### 1. Processos I/O Bound (Limitados por Entrada/Saída)

São os programas que passam a maior parte do tempo em **surtos de E/S** e pouquíssimo tempo calculando coisas na CPU.
- **O comportamento:** Eles usam a CPU por uma fração de segundo e já param, esperando algo externo.
    
- **Exemplos:** Um editor de texto (esperando você digitar), um navegador web (esperando a página baixar) ou um sistema de banco de dados (esperando o disco rígido buscar a informação)
    
- **O gargalo:** A velocidade desses programas não depende de quão rápido é o seu processador, mas sim de quão rápido é o seu SSD ou a sua internet.

### 2. Processos CPU Bound (Limitados pela CPU)

São os programas que passam a maior parte do tempo no **surto de CPU**, fazendo cálculos matemáticos pesados e quase não precisam de operações externas
  
- **O comportamento:** Eles pegam os dados e ficam "moendo" informação no processador sem parar. Eles quase não vão para a fila de "Bloqueado", só saem da CPU quando o escalonador os obriga (pelo fim da fatia de tempo).
    
- **Exemplos:** Renderizar um vídeo em 4K, treinar uma Inteligência Artificial, minerar criptomoedas ou compactar um arquivo ZIP muito grande.
    
- **O gargalo:** Aqui o que limita a velocidade é puramente o poder de fogo do seu processador.

### O Truque do Escalonador
Saber se um processo é _CPU Bound_ ou _I/O Bound_ é tão importante que os escalonadores modernos tentam "adivinhar" isso enquanto os programas estão rodando.

  
**O Sistema Operacional costuma dar prioridade muito maior para os processos I/O Bound.**
Por quê? Porque o processo _I/O Bound_ só quer usar a CPU por 1 milissegundo para mandar um comando para a tela ou pro disco, e logo em seguida ele vai dormir de novo. Se o sistema deixar ele esperando na fila atrás de um processo _CPU Bound_ gigante, o seu mouse começa a travar, o teclado fica com atraso e o computador parece lento, mesmo que só uma aba do processador esteja sendo usada!

**Os Objetivos do Escalonador:**

- **Justiça:** Garantir que todo processo tenha sua vez (evitando que algum morra de fome na fila).
- **Aplicação da Política:** Respeitar as prioridades e regras estabelecidas pelo sistema ou pelo usuário.
- **Equilíbrio:** Misturar bem processos _CPU Bound_ e _I/O Bound_ para manter tanto o processador quanto o disco rígido trabalhando de forma eficiente.

# Classificação de algoritmos de escalonamento
precisamos dividir isso em duas partes: **como** o escalonador age (Preemptivo vs. Não Preemptivo) e **onde** ele está trabalhando (Lote, Interativo, Tempo Real).

## O Comportamento: Preemptivo vs. Não Preemptivo
A palavra "preempção" vem do ato de _interromper_ ou _tomar o controle_. Isso define a "personalidade" do escalonador.

- **Não Preemptivo (O Educado):** Nesse modelo, quando o escalonador dá a CPU para um processo, ele **não pode tomar de volta**. O processo é dono da CPU até que ele decida parar por conta própria (seja porque terminou, ou porque precisou fazer uma operação de E/S, como ler um arquivo).
    
    - _O problema:_ Se um processo entrar em um _loop_ infinito ou for muito longo, o computador inteiro trava, pois o Sistema Operacional não tem o poder de arrancá-lo de lá. O Windows 3.1 usava isso!
        
- **Preemptivo (O Ditador):** Aqui o escalonador manda na casa. Ele dá a CPU para o processo, mas impõe um limite de tempo estrito (o _Quantum_ ou fatia de tempo). Se o tempo acabar e o processo ainda estiver rodando, o Sistema Operacional **arranca a CPU à força** daquele processo (usando uma interrupção de hardware), salva o estado dele e coloca o próximo da fila para rodar.
	
	- justamente o conceito de **Time Sharing** (fatia de tempo), este só pode ser aplicado em algoritmos preemptivos
    
    - _A vantagem:_ É o que permite que o seu computador moderno não trave por completo se um único programa parar de responder. É a base da multitarefa que usamos hoje

## Categorias
então na questaõ das categorias....

os sistemas em lote são aqueles com processos predominantemente cpu bound, já que seu foco é a vazão, ou seja, o maior número de processamento em menos tempo, eles são algoritmos não-preemtivos

os sistemas interativos utiliza de uma algoritmo preemptivo, já que seu foco é a operacionalidade do sistema e capacidade de processamento "simultaneo", com a utilização do time sharing

já os sistemas em tempo real, funcionam com um podelo de algoritmo preemptivo, pois neste tipo de sistema os dados devem ser processados e utilizadoos no tempo correto , já que neste casos dados avulsos ou fora de tempo, perdem totalmente sua utilidade ou sentido, assim, é necessário uma alto poder de gerenciamento da cpu a fim da entrega dos dados no tempo correto 