
==🔵**SELECT** "função embutida" **FROM** "tabela" **WHERE** "condição"==
```c
SELECT SUN(salário) FROM empregado;
SELECT MIN(data_nasc) FROM empregado WHERE lsexo = 'M';
SELECT COUNT(*) FROM empregado;
SELECT AVG(salário) FROM empregado WHERE dpto = 125;
SELECT dpto, COUNT(*), AVG(salário) FROM empregado GROUP BY dpto;
//para cada departamento selecione o codigo do departamento, a quantidade de empregados e a média salarial
SELECT dpto, COUNT(*) AS "Total de empregados", AVG(salário) FROM empregado GROUP BY dpto;
SELECT sexo, COUNT(*) FROM empregado GROUP BY sexo;
//mostra quantas pessoas tem em cada categoria do sexo ('M' e 'F')
SELECT dpto, salário FROM empregado GROUP BY dpto HAVING SUN(salário)>1000;
//para cada departamento que a soma dos salários pagos é maior que 1000 liste o departamento e o valor pago.
SELECT nome FROM empregado WHERE salario BETWEEN 1000 AND 2000;
SELECT * FROM empregado WHERE data_nasc BETWEEN (19700101 AND 19791231);
//mostra as pessoas que nasceram na década de 70
SELECT matrícula, nome FROM empregado WHERE nome LIKE '%Silva';
//ve se o ultimo nome do meu empregado termina com silva
SELECT código,nome FROM empregado WHERE codigo NOT LIKE '_A%'
//exibir codigo e nomes de todos os empregados, exceto os que tem 'A' no segundo caractere

```

### **Funções Embutidas**
-  ***SUN()*** - soma todos os valores do campo definido
-  ***MIN()*** - retorna o maior valor
-  ***MAX()*** - retorna o maior valor
-  ***COUNT()*** - conta as linhas especificadas (conta coisas repetidas pois ele olha só a linha e não o conteúdo)
-  ***AVG()*** - calcula a média do campo
-  ***GROUP BY*** - agrupa linhas da consulta (PARA CADA valor da coluna aplique) .Ele sempre imprime em ordem alfabética
-  ***AS*** - nomeia as colunas para facilitar a visualização dos valores impressos no select
-  ***AND*** - função de **E** 
-  ***OR*** - função de **OU**
- ***HAVING*** - só é usada junto com o GROUP BY, essa função serve pra dizer um limite de agrupamento, por exemplo agrupe os salários maiores que 1000, é uma condição para que o agrupamento aconteça.
	- ATENÇÃO: *o HAVING so serve pra funções de agregação (COUNT, SUM, AVG)*

### **Operadores de Comparação**
- **BETWEEN** - entre
- **LIKE** - é o 'como' ou 'semelhante a'. É usado em strings e tem o carácter % e o __ reservados. Onde o % substitui um intervalo, já o __ substitui um só caractere
-  **YEAR, MONTH, DAY** - seleciona apenas o ano, mês ou dia de uma data 
- ***DISTINCT*** remove repetições da seleção

### **Operadores Aritiméticos**
### **+, -, \*, /**

---
# criação de um BD
para acriação de um banco fazemos as seguintes etapas:

- CREATE DATABASE banco
- USE banco
- criamos as tabelas CREATE TABLE tabela
- especificamos os campos das tabelas com tipos, tamanhos e definições
- preenchemos as tabelas com dados INSERT INTO tabela
- verificamos se deu certo DESC
- fazemos buscas e checagens SELECT
- casos de erro apagamos a tabela DROP tabela

***veja o emeplo:***
```sql
CREATE DATABASE bd_escola;

USE bd_escola;

CREATE TABLE NotasAluno (
matricula INT AUTO_INCREMENT PRIMARY KEY,
nome VARCHAR(50),
data_nasc DATE,
nota_redes FLOAT DEFAULT 0
) AUTO_INCREMENT = 1111;

DESC bd_escola;

INSERT INTO NotasAluno(nome, data_nasc, nota_bd, nota_poo, nota_redes)
VALUES ('Taffarel', '1988/10/10', 6.8, 5.1, 9.2),
VALUES ('Jorginho', '1988/09/09', 7.6, 4.8, 8),
VALUES ('Aldair', '1988/08/08', 8.7, 4.7, 7),
VALUES ('Márcio', '1987/06/06', 8.6, 6.2, 6.5);

```


---


# Guia de Estudos Completo: SQL, DDL, DQL e Detalhes de Sintaxe


- Subgrupos da Linguagem SQL (DDL, DML, DQL, DCL, DTL)
    
- Comandos Estruturais e Restrições (`DROP`, `DEFAULT`, `AUTO_INCREMENT`, `NOT NULL`)
    
- Ordenação de Dados (`ORDER BY`, `ASC` / Alfabético, `DESC`)
    
- Tratamento Avançado de Nulos (`NULL`, `NOT NULL`, `IS NULL`, `IS NOT NULL`)
    
- Operadores de Comparação e Padrões (`BETWEEN`, `LIKE`, `%`, `_`)
    
- Agrupamento e Agregação (`COUNT`, `SUM`, `AVG`, `MIN`, `MAX`, `GROUP BY`, `HAVING`)


## 1. Subgrupos da Linguagem SQL

- **DDL (Data Definition Language):** Opera sobre o **esquema e estrutura** dos objetos do banco de dados (tabelas, índices, visões, bancos).
    
    - Comandos: `CREATE`, `ALTER`, `DROP`, `CREATE INDEX`.
        
- **DML (Data Manipulation Language):** Opera sobre os **dados/registros** contidos nas tabelas.
    
    - Comandos: `INSERT`, `UPDATE`, `DELETE`.
        
- **DQL (Data Query Language):** Focado exclusivamente na **consulta e seleção** de dados.
    
    - Comando: `SELECT`.


---
# Detalhamento de Comandos e Restrições Específicas

### A. O Comando `DROP` (Exclusão Estrutural - DDL)
> Remove completamente uma estrutura do banco de dados (tabela, visão, índice ou o próprio banco de dados), apagando tanto a definição/esquema quanto todos os dados contidos nela.
    
- **Sintaxe:**
```sql
    DROP TABLE Empregado;
    DROP DATABASE EmpresaBD;
    DROP INDEX idx_nome_cliente;
```


- **Diferença Crucial de Prova (`DROP` vs `DELETE` vs `TRUNCATE`):**
    - `DROP TABLE`: Apaga a **tabela inteira** e sua estrutura da base de dados. A tabela deixa de existir. (Comando DDL)
        
    - `DELETE FROM`: Apaga **linhas/registros** específicos (com `WHERE`) ou todos os registros, mas a **estrutura da tabela permanece intacta**. (Comando DML)
        
    - `TRUNCATE TABLE`: Apaga **todos os registros** de forma rápida, mantendo a estrutura da tabela pronta para novos inserts. (Comando DDL)
        

### B. Ordenação: `ASC` (Ascendente/Alfabético) e `DESC` (Decrescente)
> A cláusula `ORDER BY` é utilizada para classificar o resultado de uma consulta `SELECT`.

- **`ASC` (Ascendente):**
    - **Números:** Do menor para o maior ($1 \rightarrow 100$).
    - **Texto (Alfabético):** De 'A' a 'Z'.
    - **Datas:** Da data mais antiga para a mais recente.
        
    - **Comportamento Padrão (**_**Default**_**):** Se você omitir a palavra `ASC` ou `DESC`, a SQL aplicará **automaticamente `ASC`**.
        
- **`DESC` (Decrescente/Descendente):**
    - **Números:** Do maior para o menor ($100 \rightarrow 1$).
    - **Texto:** De 'Z' a 'A'.
    - **Datas:** Da data mais recente para a mais antiga.

- **Exemplo de Ordenação Múltipla:**
```sql
    SELECT nome, depto, salario
    FROM Empregado
    ORDER BY depto ASC, salario DESC;
```

_Neste exemplo, os empregados são agrupados por departamento em ordem alfabética/crescente (`ASC`). Se houver empate no departamento, o critério de desempate será o salário do maior para o menor (`DESC`)._


### C. `AUTO_INCREMENT` (Incremento Automático)
> Uma propriedade aplicada a uma coluna numéricas (geralmente a chave primária `PRIMARY KEY`) na criação da tabela (`DDL`).
    
- **Funcionamento:** Toda vez que um novo registro é inserido (`INSERT`), o SGBD gera automaticamente o próximo número inteiro da sequência ($1, 2, 3, 4 \dots$) sem que o usuário precise informar o valor dessa coluna.
    
- **Sintaxe na Criação (MySQL):**
```sql
    CREATE TABLE Cliente (
        id_cliente INT AUTO_INCREMENT PRIMARY KEY,
        nome VARCHAR(100) NOT NULL
    );
```
    
- **No `INSERT`:**
    
    -- Não é necessário passar a coluna id_cliente!
    INSERT INTO Cliente (nome) VALUES ('Maria Silva');

_(Nota para a prova: Diferentes SGBDs usam nomenclaturas distintas: MySQL usa `AUTO_INCREMENT`, PostgreSQL usa `SERIAL` ou `IDENTITY`, e SQL Server usa `IDENTITY(1,1)`)._


### D. Restrição `DEFAULT` (Valor Padrão)
> Define um valor pré-determinado que será gravado automaticamente na coluna caso o comando `INSERT` omita esse campo.
    
- **Aplicação:** Evitar valores nulos ou garantir valores padrão do sistema (como status padrão ou data/hora atual).
    
- **Sintaxe na Criação da Tabela:**
    
``` sql
    CREATE TABLE Pedido (
        id_pedido INT AUTO_INCREMENT PRIMARY KEY,
        data_pedido DATE DEFAULT CURRENT_DATE,
        status VARCHAR(20) DEFAULT 'Pendente'
    );
```
    
- **Exemplo Prático:** Se executarmos `INSERT INTO Pedido (id_pedido) VALUES (1);`, a coluna `status` receberá automaticamente o valor `'Pendente'`

### E. Entendendo `NULL` e `NOT NULL` de Ponta a Ponta

#### 1. Na Criação de Tabelas (DDL)

- **`NOT NULL`:** Restrição de coluna que obriga o preenchimento do campo. Tentar inserir um registro sem essa informação gera um erro no SGBD.
    
- **`NULL` (Padrão):** Se não for especificado `NOT NULL`, a coluna aceitará valores ausentes por padrão.
    

#### 2. Na Seleção de Dados (DQL)

O valor `NULL` não é zero nem uma string vazia `''`. Ele significa **ausência de valor ou valor desconhecido**.

- **Comparação com Nulos:**
    - **Forma Incorreta (Pegadinha clássica de prova):** `WHERE salario = NULL` ou `WHERE salario != NULL` $\rightarrow$ **Invalido / Retorna FALSO.**
        
    - **Forma Correta:** `WHERE salario IS NULL` (para buscar ausentes) ou `WHERE salario IS NOT NULL` (para buscar preenchidos).

#### 3. Comportamento em Funções de Agregação

- Funções como `SUM()`, `AVG()`, `MIN()`, `MAX()` e `COUNT(nome_coluna)` **ignoram completamente** as células com valor `NULL`.
    
- **Exceção de Prova:** `COUNT(*)` conta o total de **linhas/tuplas** da tabela, independentemente de haver campos nulos nessas linhas.


---
# Resumo de Operadores de Seleção

|   |   |   |
|---|---|---|
|**Operador / Cláusula**|**Função Prática**|**Exemplo**|
|`DISTINCT`|Remove registros duplicados do resultado|`SELECT DISTINCT depto FROM Empregado;`|
|`BETWEEN a AND b`|Intervalo **inclusivo** (inclui $a$ e $b$)|`WHERE salario BETWEEN 1000 AND 2000;`|
|`LIKE '%A'`|Busca textos que **terminam** com a letra A|`WHERE nome LIKE '%Silva';`|
|`LIKE '_A%'`|Busca textos que têm a letra A na **2ª posição**|`WHERE codigo LIKE '_A%';`|
|`IS NULL`|Verifica se a coluna está **sem valor**|`WHERE supervisor IS NULL;`|
|`ORDER BY ... DESC`|Ordena o resultado em ordem **decrescente**|`WHERE idade ORDER BY idade DESC;`|
