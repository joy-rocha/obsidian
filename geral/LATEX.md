
 usar o símbolo do cifrão (`$`) para avisar que vai digitar matemática:

- **Na mesma linha (inline):** Coloque um cifrão de cada lado. Exemplo: `$x = 2$` vira $x = 2$.
- **Em um bloco centralizado:** Coloque dois cifrões. Exemplo: `$$x = 2$$` centraliza a equação na tela.


## 1. Operações Básicas e Estruturas

A regra de ouro do LaTeX é que chaves `{ }` servem para agrupar as coisas. Se você vai elevar algo a mais de um caractere, coloque entre chaves.

| **O que você quer**           | **Como digitar** | **Como fica no Obsidian** |
| ----------------------------- | ---------------- | ------------------------- |
| **Fração** (um sobre o outro) | `\frac{x}{y}`    | $\frac{x}{y}$             |
| **Elevado** (Potência)        | `x^{2}`          | $x^{2}$                   |
| **Subscrito** (Índice)        | `v_{0}`          | $v_{0}$                   |
| **Raiz Quadrada**             | `\sqrt{x}`       | $\sqrt{x}$                |
| **Raiz n-ésima**              | `\sqrt[3]{x}`    | $\sqrt[3]{x}$             |
| **Mais ou Menos**             | `\pm`            | $\pm$                     |

## 2. Vetores e Física

Na física, a notação é tudo. Você pode fazer a setinha tradicional em cima da letra ou usar o negrito, que é o padrão da maioria dos livros de Cálculo e Física (como o Halliday ou o Stewart).

| **O que você quer**                  | **Como digitar**          | **Como fica no Obsidian** |
| ------------------------------------ | ------------------------- | ------------------------- |
| **Vetor com seta**                   | `\vec{v}`                 | $\vec{v}$                 |
| **Vetor em negrito**                 | `\mathbf{v}`              | $\mathbf{v}$              |
| **Versor** (Vetor unitário / Chapéu) | `\hat{i}`                 | $\hat{i}$                 |
| **Produto Escalar** (Ponto)          | `\cdot`                   | $\cdot$                   |
| **Produto Vetorial** (Cruz)          | `\times`                  | $\times$                  |
| **Letras Gregas** (ex: Teta, Pi)     | `\theta`, `\pi`, `\omega` | $\theta$, $\pi$, $\omega$ |
## 3. Conjuntos Numéricos e Lógica

Para as disciplinas de Álgebra Linear e Cálculo, você vai precisar muito desses símbolos para definir domínios. Aquele "R" estilizado dos Reais é feito com o comando `\mathbb`.

| **O que você quer**         | **Como digitar** | **Como fica no Obsidian** |
| --------------------------- | ---------------- | ------------------------- |
| **Números Reais**           | `\mathbb{R}`     | $\mathbb{R}$              |
| **Números Inteiros**        | `\mathbb{Z}`     | $\mathbb{Z}$              |
| **Pertence / Não pertence** | `\in` / `\notin` | $\in$ / $\notin$          |
| **Diferente**               | `\neq`           | $\neq$                    |
| **Aproximadamente**         | `\approx`        | $\approx$                 |
| **Infinito**                | `\infty`         | $\infty$                  |
| **vazio**                   | \emptyset        | $\emptyset$               |
|                             |                  |                           |

## 4. Funções Trigonométricas e Cálculo

**Dica importante:** No LaTeX, sempre coloque uma barra `\` antes de funções como seno e cosseno. Se você escrever apenas `cos(x)`, o programa acha que são as variáveis $c \cdot o \cdot s \cdot (x)$ multiplicadas e a fonte fica itálica. Com a barra, ele formata como função.

|**O que você quer**|**Como digitar**|**Como fica no Obsidian**|
|---|---|---|
|**Cosseno, Seno, Tangente**|`\cos(x)`, `\sin(x)`, `\tan(x)`|$\cos(x)$, $\sin(x)$, $\tan(x)$|
|**Integral** (com limites)|`\int_{a}^{b} f(x) \, dx`|$\int_{a}^{b} f(x) \, dx$|
|**Somatório**|`\sum_{i=1}^{n} x_i`|$\sum_{i=1}^{n} x_i$|
|**Limite**|`\lim_{x \to 0} \frac{1}{x}`|$\lim_{x \to 0} \frac{1}{x}$|
