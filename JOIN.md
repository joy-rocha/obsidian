
Você deve escolher o tipo de `JOIN` com base em **quais dados você quer manter no resultado** quando houver registros sem correspondência na outra tabela.

Aqui está o guia direto de quando usar cada um:

| Tipo de JOIN     | Quando usar?                                                                                                                   | O que acontece com os dados sem par?                                 |
| ---------------- | ------------------------------------------------------------------------------------------------------------------------------ | -------------------------------------------------------------------- |
| **`INNER JOIN`** | Quando você precisa de uma **correspondência exata** entre as duas tabelas.                                                    | Registros sem par são **excluídos** do resultado.                    |
| **`LEFT JOIN`**  | Quando a tabela da **esquerda é a principal** e você quer ver todos os dados dela, mesmo os que não têm relação com a direita. | Registros sem par na direita aparecem como **`NULL`**.               |
| **`RIGHT JOIN`** | Quando a tabela da **direita é a principal** (é o inverso do LEFT, usado com menos frequência por legibilidade).               | Registros sem par na esquerda aparecem como **`NULL`**.              |
| **`FULL JOIN`**  | Quando você quer fazer um **mapeamento total** e trazer absolutamente tudo de ambas as tabelas, combinando o que for possível. | Registros sem par em qualquer um dos lados aparecem como **`NULL`**. |

---

💡 Exemplos Práticos para Decidir

- **Cenário 1: "Quero uma lista de pedidos com os nomes dos clientes."**
    - **Use `INNER JOIN`**. Se um pedido não tem cliente (ou um cliente não tem pedido), ele não te interessa nessa listagem. Você só quer a intersecção perfeita.
- **Cenário 2: "Quero um relatório de todos os produtos da loja e a quantidade vendida de cada um."**
    - **Use `LEFT JOIN`** (Produtos na esquerda, Vendas na direita). Se você usar o `INNER`, os produtos que **nunca foram vendidos** vão sumir do relatório. Com o `LEFT`, eles aparecem com a quantidade como `NULL` (ou zero).
- **Cenário 3: "Quero ver todos os alunos e todos os cursos disponíveis na escola, sem exceção."**
    - **Use `FULL OUTER JOIN`**. Assim você vê alunos que não se matricularam em nada e também cursos que ainda estão vazios, tudo na mesma consulta.

Quer que eu monte um exemplo de código comparando o resultado de um **INNER JOIN** e de um **LEFT JOIN** usando o seu cenário de **clientes e pedidos** para ficar mais claro?