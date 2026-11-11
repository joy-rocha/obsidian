**HAVING** no SQL serve para **filtrar os resultados de grupos de dados** depois que eles já foram agrupados

O **UPDATE** altera apenas **registros que já existem** em uma tabela de banco de dados

**MODIFY** funciona como uma operação "inteligente" que **atualiza o registro se ele existir ou cria um novo**

O comando `DECIMAL(6, 2)` (também chamado de `NUMERIC(6, 2)`) define um tipo de dado numérico exato que armazena números com um total de **6 dígitos**, sendo **2 deles destinados às casas decimais**

- **`VARCHAR(n)`:** Armazena textos de tamanho variável (até o limite `n` de caracteres especificado). O banco de dados consome espaço apenas com os caracteres que você realmente salvar, mais alguns bytes de controle. Exemplo: `nome VARCHAR(100)`

- **`CHAR(n)`:** Armazena textos de tamanho **fixo**. Se você definir `CHAR(10)` e salvar apenas 3 letras, o banco de dados vai preencher o restante com espaços em branco até completar os 10. É indicado apenas para dados que têm sempre o mesmo tamanho (ex: siglas de estados `UF CHAR(2)` ou códigos postais)

- **`TEXT`:** Utilizado para **textos muito longos** (artigos, comentários extensos, descrições grandes) que ultrapassam os limites padrão do `VARCHAR`. Geralmente não é ideal para campos que precisam de buscas rápidas ou indexação frequente

____
```sql
CREATE TABLE T (
    a INT NOT NULL,
    b VARCHAR(100),
    c CHAR(1),
    PRIMARY KEY (a)
);

CREATE TABLE S (
    g INT,
    h INT,
    i INT,
    PRIMARY KEY (g, h),
    FOREIGN KEY (g) REFERENCES T(a),
    FOREIGN KEY (h) REFERENCES R(d)
);
```
___

Quando você tem um `SELECT` dentro de outro comando SQL (como outro `SELECT`, `UPDATE`, `DELETE` ou `INSERT`), isso caracteriza uma subconsulta.

o `IN` é um `OR` que suporta varias condições de uma só vez

```SQL
"COM OR"
SELECT nome FROM produtos 
WHERE categoria_id = 1 OR categoria_id = 2 OR categoria_id = 5;

"COM IN"
SELECT nome FROM produtos 
WHERE categoria_id IN (1, 2, 5);
```



---
# 6) 

a -      SELECT produto.nome, AVG(COUNT(itemPediso.quantidade))
    FROM itemPedido 
     INNER JOIN produto 
     ON produto.idproduto = intemPedido.produto_fk;

b -      SELECT produto.nome AS 'produto', fornecedor.nome
		FROM produto
		RIGHT JOIN fornecedor 
		ON produto.fornecedor_fk = fornecedor.idfornecedor;

c - SELECT cliente.nome AS 'cliente'
	FROM itemPedido
	INNER JOIN pedido
	ON (ItemPedido.pedido_fk = pedido.idpedido) AND ItemPedido.quantidade>3,
	INNER JOIN cliente
	ON pedido.cliente_fk = cliente.idcliente
	

---
- **`ON`:** Usado logo após o `JOIN` exclusivamente para dizer qual coluna liga uma tabela à outra (Chave Primária = Chave Estrangeira).
- **`WHERE`:** Usado para filtrar linhas individuais **antes** de qualquer agrupamento (ex: `WHERE Cliente.nome = 'João'` ou `WHERE ItemPedido.quantidade > 3`).
- **`AND`:** Serve para somar duas condições no mesmo lugar (ex: `WHERE cidade = 'Recife' AND preco > 10`). Não se usa `AND` no `ON` para filtrar dados da tabela, deixe os filtros para o `WHERE`.
- **`HAVING`:** Funciona igual ao `WHERE`, mas é usado **depois do `GROUP BY`** para filtrar resultados de cálculos (`COUNT`, `SUM`, `AVG`).