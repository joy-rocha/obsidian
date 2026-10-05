
Enquanto o modelo conceitual foca na realidade observada e o modelo lógico foca na estrutura de tabelas e chaves, o **Modelo Físico traz a representação dos dados de acordo com a estrutura de armazenamento concreta do SGBD** (Sistema Gerenciador de Banco de Dados, como PostgreSQL, MySQL ou Oracle)

![[Pasted image 20261002103416.png|397]]

Os **subgrupos da linguagem SQL** são divisões funcionais que organizam os comandos de acordo com o tipo de operação realizada no banco de dados. A linguagem é dividida em 5 subgrupos principais:

**DDL (_Data Definition Language_ / Linguagem de Definição de Dados)**:
- Define, altera ou exclui a estrutura do esquema e das tabelas do banco de dados.

**DML (_Data Manipulation Language_ / Linguagem de Manipulação de Dados)**:
-  Manipula os dados gravados nas tabelas (inserção, atualização e remoção de registros).

 **DQL (_Data Query Language_ / Linguagem de Seleção de Dados)**:
-  Realiza consultas e seleção de informações nas tabelas do banco.
   
**DCL (_Data Control Language_ / Linguagem de Controle de Dados)**:
-  Gerencia a segurança, os privilégios e os direitos de acesso dos usuários ao banco de dados.

**DTL (_Data Transaction Language_ / Linguagem de Transação de Dados)**:
  - Gerencia a execução das transações no banco de dados.

---


**Os comandos básicos da linguagem **SQL** são divididos de acordo com a função que desempenham no banco de dados através dos seus **subgrupos funcionais**.

---

### 1. DDL — _Data Definition Language_ (Linguagem de Definição de Dados)

Utilizada para definir, modificar ou eliminar a estrutura do banco de dados e de suas tabelas:

- **`CREATE DATABASE`**: Cria o banco de dados que armazenará as tabelas.
- **`CREATE TABLE`**: Define uma nova tabela especificando o nome das colunas, seus tipos de dados e restrições de integridade (como `PRIMARY KEY`, `FOREIGN KEY`, `NOT NULL`, `UNIQUE`, `CHECK` e `DEFAULT`).
- **`ALTER TABLE`**: Modifica a estrutura de uma tabela existente, permitindo adicionar colunas (`ADD`), alterar tipos ou nomes de atributos (`MODIFY COLUMN`, `RENAME`) ou adicionar restrições (`ADD CONSTRAINT`).
- **`DROP TABLE`**: Exclui definitivamente uma tabela do banco de dados, removendo sua estrutura e todos os dados gravados nela.

---

### 2. DML — _Data Manipulation Language_ (Linguagem de Manipulação de Dados)

Responsável por manipular os registros gravados nas tabelas:

- **`INSERT`**: Insere novos registros/linhas de dados na tabela.
- **`UPDATE`**: Modifica ou atualiza dados que já estão armazenados na tabela.
- **`DELETE`**: Exclui um ou mais registros/linhas de uma tabela.

---

### 3. DQL — _Data Query Language_ (Linguagem de Seleção de Dados)

Responsável exclusivamente por realizar consultas e seleção de informações nas tabelas:

- **`SELECT`**: Recupera dados das tabelas. É frequentemente acompanhado por:
    - **Filtros e Operadores**: `WHERE`, `DISTINCT`, `IS NULL`, `BETWEEN`, `LIKE`.
    - **Ordenação**: `ORDER BY` (`ASC` / `DESC`).
    - **Funções Embutidas**: `COUNT`, `SUM`, `AVG`, `MIN`, `MAX`.
    - **Agrupamento**: `GROUP BY` e `HAVING`.

_(Nota: Alguns autores consideram o comando `SELECT` como parte integrante da DML)._

---

### 4. DCL — _Data Control Language_ (Linguagem de Controle de Dados)

Gerencia os aspectos de segurança e privilégios de acesso dos usuários ao banco:

- **`GRANT`**: Concede direitos de acesso ou permissões a usuários.
- **`REVOKE`**: Revoga permissões anteriormente concedidas.

---

### 5. DTL — _Data Transaction Language_ (Linguagem de Transação de Dados)

Gerencia a execução e o controle de transações dentro do banco de dados:

- **`COMMIT`**: Confirma as alterações efetuadas em uma transação.
- **`ROLLBACK`**: Desfaz as alterações executadas durante a transação.
- **`SAVEPOINT`**: Estabelece um ponto de salvamento intermediário na transação.

---


