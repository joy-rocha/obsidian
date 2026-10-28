produto vetorial, multiplicação de vetores, vetores unitários, propriedade comutativa, subtração e soma de vetores
## $\vec{v}$ 
**LEI DOS COSSENOS**
   é uma regra matemática usada para achar o lado ou o ângulo de qualquer triângulo, mesmo quando ele não tem 90 graus. Ela funciona como uma extensão do Teorema de Pitágoras.

**Produto Escalar ($\cos\theta$)**
**Testar se são perpendiculares ($90^\circ$):** Se o resultado der $0$, você sabe na hora que eles fazem $90^\circ$ entre si (pois $\cos 90^\circ = 0$).


**Produto Vetorial ($\sin\theta$)**
Mede a **perpendicularidade, rotação ou área**. Quanto mais perpendiculares ($90^\circ$), maior o resultado (pois $\sin 90^\circ = 1$).

O módulo de um vetor nada mais é do que o próprio **Teorema de Pitágoras** aplicado às componentes dele.
$$\vert{}\vec{v}\vert{} = \sqrt{v_x^2 + v_y^2}$$

A **componente de um vetor** é simplesmente a "fatia" ou a parte do vetor que atua em uma direção específica (como frente/trás, esquerda/direita ou cima/baixo).

Em vez de olhar para o vetor como uma seta inteira inclinada na diagonal, nós o dividimos em passos simples ao longo dos eixos do espaço:

- **Eixo $x$ ($\hat{i}$):** É a componente horizontal (esquerda/direita).
    
- **Eixo $y$ ($\hat{j}$):** É a componente vertical no plano (frente/trás ou cima/baixo no papel).
    
- **Eixo $z$ ($\hat{k}$):** É a componente de profundidade ou altura (para fora ou para dentro da página).

### As fórmulas das componentes dependem do módulo do vetor $\vert{}\vec{v}\vert{}$ e do ângulo $\theta$:

- **Se o ângulo $\theta$ estiver no eixo $x$ (no chão):**
    
    - $v_x = \vert{}\vec{v}\vert{} \cdot \cos\theta$ (o eixo $x$ está **CO**lado $\rightarrow$ **CO**sseno)
        
    - $v_y = \vert{}\vec{v}\vert{} \cdot \sin\theta$ (o eixo $y$ está **SE**m o ângulo $\rightarrow$ **SE**no)
        
- **Se o ângulo $\theta$ estiver no eixo $y$ (na parede):**
    
    - $v_y = \vert{}\vec{v}\vert{} \cdot \cos\theta$ (agora o $y$ é quem está **CO**lado $\rightarrow$ **CO**sseno)
        
    - $v_x = \vert{}\vec{v}\vert{} \cdot \sin\theta$ (o $x$ ficou **SE**m o ângulo $\rightarrow$ **SE**no)
        

**Resumo da diferença para não confundir mais:**

- **Achar componentes (Decomposição de 1 vetor):** Olha onde o ângulo está encostado (**CO**lado = Cosseno, **SE**m = Seno).
    
- **Multiplicar 2 vetores (Produtos):**
    
    - Produto Escalar usa Cosseno ($\vec{a} \cdot \vec{b} = \vert{}\vec{a}\vert{} \vert{}\vec{b}\vert{} \cos\theta$).
        
    - Produto Vetorial usa Seno ($\vert{}\vec{a} \times \vec{b}\vert{} = \vert{}\vec{a}\vert{} \vert{}\vec{b}\vert{} \sin\theta$).

### ORDEM:  Î - Ĵ - k

| muliplicação | resultado |
| ------------ | --------- |
| $Î$ X $Ĵ$    | = $k$     |
| $Î$ X $K$    | = $-Ĵ$    |
| $ĵ$ X $Î$    | = $-k$    |
| $Ĵ$ X $K$    | = $î$     |
| $k$ X $î$    | =  $ĵ$    |
| $k$ X $Ĵ$    | = $-î$    |


# **Cinemática 1D**, **Lançamento de Projéteis (2D)**, **Movimento Circular (MCU)** e **Movimento Relativo**.

  
## 1. Cinemática Retilínea em 1D (Questões 01 a 19)

### Conceitos Fundamentais

- **Deslocamento ($\Delta x$):** Variação da posição ($\Delta x = x_f - x_0$).
    
      
    
- **Velocidade Média ($v_m$):** Razão entre o deslocamento total e o tempo total decorrido:
    
      
    
    $$v_m = \frac{\Delta x_{total}}{\Delta t_{total}}$$
    
    _Dica para Q01 e Q02:_ Nunca faça média aritmética simples de velocidades de trechos diferentes. Encontre o tempo gasto em cada trecho e use a fórmula geral acima.
    
      
    
- **Aceleração Média ($a_m$):** Taxa de variação da velocidade no tempo:
    
      
    
    $$a_m = \frac{\Delta v}{\Delta t} = \frac{v_f - v_0}{t_f - t_0}$$
    
    _Dica para Q05 e Q14:_ Atente aos sinais dos sentidos. Se o corpo muda do sentido positivo para o oposto, $v_f$ passa a ser negativo (ex: $v_0 = +18\text{ m/s}$ e $v_f = -30\text{ m/s}$, logo $\Delta v = -30 - 18 = -48\text{ m/s}$).
    
      
    

### Movimento Retilíneo Uniformemente Variado (MUV)

Ocorre quando a aceleração $a$ é constante. O conjunto de fórmulas essenciais é:

  

1. **Velocidade em função do tempo:** $v = v_0 + a t$
    
      
    
2. **Posição em função do tempo:** $x = x_0 + v_0 t + \frac{1}{2} a t^2$
    
      
    
3. **Equação de Torricelli** (usada quando o problema não fornece e não pede o tempo $t$):
    
      
    
    $$v^2 = v_0^2 + 2a\Delta x$$
    

### Queda Livre e Lançamento Vertical (Q09, Q10, Q19)

É um MUV em que a aceleração é a da gravidade ($g \approx 9,8\text{ m/s}^2$).

  

- Adote o eixo vertical $y$ apontando para cima: a aceleração será $a = -g$.
    
      
    
- Na **altura máxima** de um lançamento vertical, a velocidade instantânea é zero ($v_y = 0$).
    
      
    

### Análise Funcional e Derivadas (Q03, Q11)

Quando a posição $x(t)$ ou velocidade $v(t)$ é dada por uma equação polinomial:

  

- A velocidade é a derivada da posição: $v(t) = \frac{dx}{dt}$
    
      
    
- A aceleração é a derivada da velocidade: $a(t) = \frac{dv}{dt}$
    
      
    

## 2. Movimento em Duas Dimensões e Projéteis (Questões 20 a 29)

A regra de ouro no lançamento de projéteis é o **Princípio da Independência dos Movimentos de Galileu**: o movimento horizontal e o vertical acontecem simultaneamente, mas não interferem um no outro.

  

### Eixo Horizontal ($x$) — Movimento Uniforme (sem aceleração)

- Velocidade constante: $v_x = v_{0x} = v_0 \cdot \cos(\theta)$
    
      
    
- Posição horizontal: $x(t) = x_0 + v_x \cdot t$
    
      
    

### Eixo Vertical ($y$) — MUV (com aceleração $g$)

- Velocidade inicial vertical: $v_{0y} = v_0 \cdot \sen(\theta)$
    
      
    
- Posição vertical: $y(t) = y_0 + v_{0y} \cdot t - \frac{1}{2} g t^2$
    
      
    
- Velocidade vertical instantânea: $v_y(t) = v_{0y} - g t$
    
      
    

### Grandeza Resultante da Velocidade

Em qualquer ponto da trajetória, o módulo da velocidade é a hipotenusa dos vetores componentes:

  

$$v = \sqrt{v_x^2 + v_y^2}$$

## 3. Movimento Circular Uniforme - MCU (Questões 30 a 33)

No MCU, o objeto gira em uma trajetória circular de raio $R$ com velocidade escalar constante $v$.

  

- **Período ($T$):** Tempo necessário para completar 1 volta inteira.
    
      
    
- **Velocidade Escalar Instantânea ($v$):**
    
      
    
    $$v = \frac{2\pi R}{T}$$
    
- **Aceleração Centrípeta ($a_c$):** Aponta sempre para o **centro da curva** e serve para mudar a direção da trajetória:
    
      
    
    $$a_c = \frac{v^2}{R}$$
    
- **Relação Vetorial (Q31, Q32):** Como a aceleração centrípeta aponta para o centro e o vetor posição $\vec{r}$ aponta do centro para o corpo, os vetores têm sentidos opostos:
    
      
    
    $$\vec{a}_c = -\frac{v^2}{R^2} \vec{r}$$
    

## 4. Movimento Relativo (Questões 34 a 36)

Analisa a velocidade de um corpo $A$ vista a partir da referência de um corpo $B$.

  

- **Fórmula da Velocidade Relativa:**
    
      
    
    $$\vec{v}_{A/B} = \vec{v}_A - \vec{v}_B$$
    
    _(A velocidade de $A$ em relação a $B$ é a velocidade vetorial de $A$ menos a velocidade de $B$)._
    
      
    
- **Em componentes unitários:** Trabalhe separadamente os eixos $\hat{i}$ (horizontal) e $\hat{j}$ (vertical) para somar ou subtrair os vetores de velocidade.
    
      
    

