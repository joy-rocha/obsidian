

# BOOT
Temos 3 tipos de BOOT e podemos selecionar ele com base nesta tabela:

| BT1 | BT0 | tipo               |
| --- | --- | ------------------ |
| X   | 0   | memória flash      |
| 0   | 1   | memória do sistema |
| 1   | 1   | SRAM               |

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
**$-$ System Reset:** Reseta os dados do processador e dos periféricos (botão)
**$-$ Processor Reset:** Reseta apenas o processador (via código)

# Sistema de Clock no STM

**RCC** (_Reset and Clock Control_ ou Controle de Reset e Clock) - É o periférico fundamental responsável pelo controle do clock e do reset

![[Captura de tela de 2026-08-04 20-10-20.png|576]]

Temos 3 fontes de clk disponíveis para o SYSCLK:
$- HSI, HSE, PLL$

para o PLL temos 2 para o PLL:
$- HSE, HSI$

**FONTE SECUNDÁRIA DE CLK**
LSI e LSE  ->  Só servem para gerar o clock do relógio do microcontrolador pois só gera até 32KHz

**APÓS O SYSCLK TEMOS:**
![[Captura de tela de 2026-08-04 20-45-28.png]]
ESCVREVA AQUI DETALHADO DPSS !!!!!!!!!!!


# Sistema de distribuíção de CLK
![[Captura de tela de 2026-08-04 20-50-35.png]]
 -  Esse sistema é o mesmo que temos no CubeMX na parte de configuração de CLK