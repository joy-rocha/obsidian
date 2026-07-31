https://youtu.be/5GbzRd3vLUU?si=hCtAdMQ-T3H5zUyR

`#include <iostream>`

`int main() {`
    `int idade = 25;`
    `std::cout << "A idade é: " << idade << std::endl;`
    `return 0;`
`}`

- **`std::cout`**: Representa o fluxo de saída padrão (geralmente a tela do console).

- **Operador `<<`**: Envia os dados que estão à sua direita para o `cout` à sua esquerda. É possível encadear vários valores na mesma linha. 

- **`std::endl`**: Insere uma quebra de linha após a impressão e descarrega o buffer de saída. Também é possível usar o caractere de escape `\n` dentro das aspas para pular uma linha. 

- **`std::`**: Prefixo que indica que o elemento pertence ao namespace padrão da biblioteca do C++ (pode ser evitado adicionando `using namespace std;` no início do código).



