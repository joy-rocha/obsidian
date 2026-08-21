#### Terminologia
- cada tabela é chamada de relação
- tuplas = linhas
- atributo = colunas
- chave primária - primary key : representada pelo atributo sublinhado
- chave estrangeira 
- **domínio** da relação, tipo de dado que descreve cada um dos atributos da minha relação 
- todo atributo possui um nome, dado, e um tamanho específico
- cada tupla tem uma chave primária (um dado identificador)
- relação é um conjunto de tuplas
- descrição semântica ajuda na interpretação de seus valores (você verbalizar edescrever o domínio )
- descrição fisica é composta pelo tipo e o tamanho do dado no dimínio
- uma tabela é equivalente a uma entidade

| **matrícula** | **nome** | **saláio** | -> **ATRIBUTO** (coluna) |
| ------------- | -------- | ---------- | ------------------------ |
| 1234          | Maria    | 1.200,00   |                          |
| 4567          | José     | 800,50     | -> **TUPLA (linha)**     |
| 8901          | Zefa     | 3.500,00   |                          |

---
### **Esquema de relação**
**R(A1, A2, ...)**
ex: aluno(nome, sexo, <u>matŕcula</u> , idade)

>**R** nome da relação (tabela / entidade)
  **(A1, A2, ...)**  - **intenção da relação** (descrição dos atributos)  
  $-$ dados dos atributos são chamados de **extensão da relação**

**OBS:** Para uma mesma intenção pode existir mais de uma extensão

#### Grau de uma relação
É a quantidade de atributos que uma relação contém
#### Caracteŕsticas de uma relação
- A ordem das tuplas e dos atributos NÃO têm importância
- Todo atributo possui valor atômico (iredutível) 
-  Os atributos de uma mesma relação NÃO podem ter o mesmo nome
-  Todas as tuplas devem ser ÚNICAS (não pode ter linha repetida)

#### Chave de identificação
	 - Chave primária (simples ou composta)
	 - Chave estrangeira
	 - Chave de uma relação
		É representado pelo atributo sublinhado
