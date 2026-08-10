
# BOOT
Temos 3 tipos de BOOT e podemos selecionar ele com base nesta tabela:

| BT1 | BT0 | tipo                             |
| --- | --- | -------------------------------- |
| X   | 0   | memória flash                    |
| 0   | 1   | memória do sistema (boot loader) |
| 1   | 1   | SRAM                             |
**BOOT LOADER** - Programa que é execultado antes do nosso programa, é gravado durande a fabricação do stm, serve para permitir a programação da memória flash principal sem precisar de um gravador, ele configura uma interface de comunicação nativa, e espera que o programa a ser gravado seja fornecido por esta porta.
# Sequência de INICIALIZAÇÃO do STM

1. Leitura dos pinos Boot do microcontrolador
2. carregamento do Stack Point (R13) e garregamento do PC (R15)
3. Execução do código no Reset Handler
4. Chamada da main ao fim da execução do Reset Handler (CALL MAIN)

**APÓS O RESET O MICROCONTROLADOR RODA NA CONGIGURAÇÃO PADRÃO, QUE É:**
-  Core configurado em Single Stack (Pilha Única)
-  Acesso completo a todos os registradores (modo Thread privilegiado)
-  Clock oriundo do sistema ocilador interno de alta velocidade (16 MHz)
-  Clock conectado apeas ao processador, à memória flash, RAM e ao controlador de interrupções NVIC
-  Flash configurada para 5 estados de espera (conforme exigido para operação de clock de até 168 MHz)

# Tipos de RESET

**$-$ Power on Reset:** Reseta tudo, ocorre quando desligamos/ligamos o sistema (alimentação)
**$-$ System Reset:** Reseta os dados do processador e dos periféricos, mantém o circuito de depuração (botão)
**$-$ Processor Reset:** Reseta apenas o processador (via código)

# Sistema de Clock no STM

**RCC** (_Reset and Clock Control_ ou Controle de Reset e Clock) - É o periférico fundamental responsável pelo controle do clock e do reset, controla a ativação dos circuitos ociladores, a distribuição dos clocks e o gerenciamento dos resets

![[Captura de tela de 2026-08-04 20-10-20.png|576]]

### SYSCLK
Temos 3 fontes de clk disponíveis para o SYSCLK, que pode ter uma saída de até 168 MHz:
- **HSI** (ociador interno de 16MHz)
- **HSE** (ocilador externo de alta velocidade, pode ser gerado por meio de duas fontes externas, um sinal de clk externo, ou atraves de um cristal)
- **PLL** (multiplicador de frequência)

### PLL Source
O sinal de saída do MUX $PLL Source$ passa por um disisor de frequênia (fator de divisão M). A partir deste divisor ele pode enviar o sinal para o $PLL$ ou para o $PLLI2S$

### PLL
É um circuito multiplicador de frequência que pode ser alimentado por duas fontes de clk, $HSE$ e $HSI$, que são selecionados pelo mux $PLL Source$.  O $PLL$ apresenta dois sinais de saída diferentes o S1 (até 168 MHz) que está conectado ao SYSCLK e a S2 (fixo, 48MHz) gerados para USB, RNG, SDIO.

**PARÂMETROS DE CONFIGURAÇÃO DO PLL:**
$-$fator de multiplicação $N$
$-$fator de divisão da saída $S1$ que é o $P$
$-$fator de divisão da saída $S2$ que é o $Q$

### **PLL I2S dedicado**
possui bits dedicados de configuração: fator de multiplicação $M$ e o fator de divisão $R$

### FONTE SECUNDÁRIA DE CLK
$LSI$ e $LSE$  ->  Só servem para gerar o clock do relógio do microcontrolador pois só gera até 32KHz

-> cole a foto do bloquinho que representa isso aquii

### APÓS O SYSCLK TEMOS:
![[Captura de tela de 2026-08-04 20-45-28.png]]
ESCVREVA AQUI DETALHADO DPSS !!!!!!!!!!!


# Sistema geral de distribuíção de CLK
![[Captura de tela de 2026-08-04 20-50-35.png]]
 -  Esse sistema é o mesmo que temos no CubeMX na parte de configuração de CLK