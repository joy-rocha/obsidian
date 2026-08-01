MCU - microcontrolador

- Para placas não oficiais criamos o projeto a partir de uma MCU, selecionamos o chip desejado para a inicialização do projeto.

![[Pasted image 20260730091446.png|190]]

LQFP**100** - esse 100 indica que temos 100 barramentos no chip

# CONFIGURAR O CLOCK

Pinout & Configuration -> SystemCore -> RCC -> 1primeira op e coloca crystal ceramic -> clock configuration -> PLL Source MUX -> marca a caixinha HSE -> em input frequency digite 8 -> vai no campo HCLK e bota 168 -> Project Manager -> faz as configurações -> Grenerate Code -> open project

-> quanto mais rápido o clock mais ele consome energia
-> escreva sempre entre os comentários BEGIN e END

**1° ATV - Piscar um LED**
RCC->AHB1ENR |= RCC_AHB1ENR_GPIOAEN;
GPIOA->MODER |=0b01 <<12;



