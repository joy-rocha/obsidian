
---
## 1. Conceitos Básicos e Origem

- **SQL (_Structured Query Language_)**: Linguagem padrão para consulta, manipulação e gerenciamento de bancos de dados relacionais.

- **História**: Desenvolvida na década de 1970 por **Donald D. Chamberlin** e Raymond Boyce nos laboratórios da IBM. Seu conceito foi fundamentado no modelo relacional proposto por **Edgar F. Codd**.

---
## 2. Categorias de Comandos SQL

A linguagem SQL é dividida em subgrupos principais segundo a finalidade de cada comando:

|**Categoria**|**Sigla**|**Função Principal**|**Principais Comandos**|
|---|---|---|---|
|**Data Definition Language**|**DDL**|Define e altera a estrutura das tabelas e objetos.|`CREATE`, `ALTER`, `DROP`|
|**Data Manipulation Language**|**DML**|Manipula e altera os dados dentro das tabelas.|`INSERT`, `UPDATE`, `DELETE`|
|**Data Control Language**|**DCL**|Controla os privilégios e permissões de acesso dos usuários.|`GRANT`, `REVOKE`|
|**Data Query Language**|**DQL**|Efetua leituras e consultas aos dados armazenados.|`SELECT`|

---
## 3. Definição de Estruturas (DDL)
Comandos usados para modificar a estrutura do banco de dados (tabelas, colunas, índices):

- **`CREATE TABLE`**: Cria uma nova tabela no banco de dados.

- **`ALTER TABLE`**: Modifica a estrutura de uma tabela existente (ex.: adicionar ou alterar colunas).
```sql
-- Exemplo: Adicionando uma nova coluna
ALTER TABLE tblLivros ADD ID_editora SMALLINT NOT NULL;
```

- **`DROP TABLE`**: Remove definitivamente toda a estrutura da tabela e seus dados do banco.
```sql
-- Exemplo: Excluindo uma tabela
DROP TABLE tblDisciplina;
```

---
## 4. Manipulação de Dados (DML)
Comandos para inserir, modificar ou remover os registros das tabelas:

- **`INSERT INTO`**: Insere novos registros numa tabela.
```sql
INSERT INTO tblAutores (SobrenomeAutor) VALUES ('Date');
```

- **`UPDATE`**: Atualiza dados já existentes em um ou mais registros.
```sql
UPDATE tblClientes SET NomeCliente = 'Iana' WHERE ID = 1;
```

- **`DELETE`**: Apaga linhas específicas de uma tabela (mantendo a estrutura da tabela intacta).
```sql
DELETE FROM tblClientes WHERE ID = 1;
```

---
## 5. Consultas e Filtros de Dados (DQL)
A instrução **`SELECT`** é o comando central para realizar buscas no banco de dados.

### Seleção Básica

- **Selecionar todas as colunas (`*`)**:
```sql
SELECT * FROM tblClientes;
```

- **Selecionar colunas específicas**:
```sql
SELECT NomeCliente FROM tblClientes;
```

- **Eliminar duplicados (`DISTINCT`)**: Retorna apenas valores únicos de uma coluna.
```sql
SELECT DISTINCT AreaAtuacao FROM tblProfessores;
```


### Filtragem (`WHERE`)
A cláusula `WHERE` especifica as condições que os registros devem cumprir para serem retornados. Operadores como `AND` e `OR` combinam múltiplas condições:

```sql
SELECT * FROM tblProfessores 
WHERE NomeProfessor = 'Iana' AND SobrenomeProfessor = 'Passos';
```

### Padrões de Texto (`LIKE` e Marcadores Curinga)
O operador `LIKE` é utilizado no `WHERE` para buscar padrões em cadeias de caracteres:

- **`'M%'`**: Textos que **iniciam** com a letra M.
- **`'%M'`**: Textos que **terminam** com a letra M.
- **`'%Araújo%'`**: Textos que contêm "Araújo" em **qualquer posição**.

```sql
SELECT NomeAluno, MatricAluno 
FROM tblAlunos 
WHERE SobrenomeAluno LIKE '%Araújo%';
```

### Ordenação (`ORDER BY`)
Classifica os resultados por ordem crescente (padrão) ou decrescente:

```sql
SELECT * FROM tblClientes ORDER BY NomeCliente;
```

---
## 6. Agrupamento e Funções de Agregação

### Funções de Agregação (Funções Embutidas)
Realizam um cálculo sobre um conjunto de valores e retornam um único valor resumido:

- **`AVG()`**: Calcula a média aritmética dos valores numéricos.
- **`COUNT()`**: Conta o número total de registros ou linhas.
- **`SUM()`**: Soma todos os valores da coluna.
- **`MAX()` / `MIN()`**: Retorna o valor máximo ou mínimo.

### Agrupamento (`GROUP BY` e `HAVING`)

- **`GROUP BY`**: Agrupa registros que possuem os mesmos valores em colunas específicas para permitir cálculos agregados por grupo.
    
- **`HAVING`**: Aplica condições de filtragem aos **grupos já formados** (diferente do `WHERE`, que filtra linhas individuais antes do agrupamento).

```sql
SELECT AreaAtuacao, AVG(Salario) 
FROM tblProfessores 
GROUP BY AreaAtuacao 
HAVING AVG(Salario) > 3000;
```

---
## 7. Restrições de Dados (_Constraints_)
As restrições são regras aplicadas às colunas para garantir a integridade, consistência e precisão dos dados inseridos na tabela:

- **`PRIMARY KEY`**: Identifica exclusivamente cada registro da tabela (não aceita valores nulos ou duplicados).

- **`FOREIGN KEY`**: Mantém a integridade referencial estabelecendo uma ligação com a chave primária de outra tabela.

- **`NOT NULL`**: Garante que uma coluna não aceite valores nulos/vazios.

- **`UNIQUE`**: Assegura que todos os valores em uma coluna sejam distintos entre si.

- **`CHECK`**: Valida se o valor inserido cumpre uma condição lógica pré-definida.

- **`DEFAULT`**: Preenche a coluna com um valor padrão predefinido caso nenhum valor seja informado no momento do cadastro.

---
