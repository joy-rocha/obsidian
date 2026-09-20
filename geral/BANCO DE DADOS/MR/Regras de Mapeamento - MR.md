#### 1) Entidades regurales ou fortes 
-  Para cada entidade regular $E$ noesquema ER, cria-se uma relação $R$ que inclui os atributos simples de $E$
-  Para cada atributo composto cria-se atributos mais sinmples, ele meio que separa em partes menores, **decomposmos em atributos simples**
- Atributos múltivalorados não são representados, pois um atributo tem que ser atômico 
#### 2) Entidades fracas
-  Para cada entidade fraca $E$, cria-se uma relação $R$ e incluí-se todos os atributos simples de $E$
-  E acrescentamos um atributo $\text{chave estrangeira}$ em $R$, que vai ser a  mesma $\text{chave primária}$ de $E$
-  Foren key - bote _fk depois do nome da chave estrangeira para representá-la
-  A Chave Primária de uma relação FRACA é uma chave composta, que é a primária e a estrangeira separada por vírgula e ambas sublinhadas
#### 3) Relacionamentos 1:1 
-  Identifica-se as relações $S$ e $T$ que correspondem às ebtidades que participam do relacionamento
-  Para ser $S$ vc escolhe a entidade que tem participação total no relacionamento
-  Inclui todos os atributos dos relacionamento como atributos de $S$ 
#### 4) Relacionamentos 1:n
- identifica a relação $S$ que representa a entidadeque participa do lado $N$ do relacionamento
- incluí-to, se como chave estrangeira  $S$ ...... (falta terminar de copiar essa parte aqui hein ) 
#### 5) Relacionamentos n:1

#### 6) Relacionamentos n:n
-  Cria-se uma nova relação $S$  para representar o relacionamento
-  Inclui como chave estrangeira de s as chaves promárias das relações que participam do relacionameno. A combinação dessas chaves formará a chave primária da relação $S$
-  ...


## REGRA 1:
> ENTIDADES FORTES
- PARA TODA ENTIDADE FORTE CRIAMOS UM RELAÇÃO (UMA TABELA)
- ADCIONAMOS TODOS OS ATRIBUTOS **SIMPLES**
- SE TIVER ALGUM ATRIBUTO COMPOSTO DERIVAMOS ELE
- DEFINIMOS AS CHAVES PRIMÁRIA E ESTRANGEIRA SE HOUVER

## REGRA 2:
> ENTIDADES FRACAS
- CRIAMOS UMA RELAÇÃO
- ESCREVEMOS TODOS OS ATRIBUTOS SIMPLES
- RECEBE COMO CHAVE ESTRANGEIRA É A PRIMARIA DE QUEM ELA DEPENDE
- AS CHAVES PRIMÁRIAS SÃO A ORIGINAL E A ESTRANGERIRA TBM (a FK é primária tbm)
- ATRIBUTOS MULTIVALORADOS VIRAM UMA RELAÇÃO, E RECEBE A CHAVE PRIMÁRIA DA TABELA DE ONDE ELE VEIO E O PRÓRIO ATRIBUTO (*chave primária sempre é composta: uma chave formada por vários atributos*)

## REGRA 3, 4, 5 e 6:
> *RELACIONAMENTOS*

### 1:N
> IDENTIFICA A RELAÇÃO COM PARTICIPAÇÃO TOTALNO RELACIONAMENTO, SE N TIVER NEHUMA TOTAL , TANTO FAZ A ESCOLHA
- O LADO N RECEBE O ATRIBUTO
-  A CHAVE PRIMÁRIA DO LADO N É A CHAVE DO LADO UM

### N:N
> VIRAM UMA NOVA RELAÇÃO (TABELA)
- AS CHAVES PRIMÁRIAS DAS RELAÇÕES QUE SE REACIONAM COMO CHAVE ESTRANGEIRA
- ATRIBUTOS DOS RELACIONAMENTOS SÃO ACOLHIDOS PELA NOVA RELAÇÃO
- POSSUEM CHAVE PRIMÁRIA COMPOSTA
- 





# ATIVIDADE 04 
DEPENDENTE(CEP, Numero, Nome, <u> RG , Matricula_FK)</u>;
USUARIO( <u>Matricula </u> ,Nome, Sexo, Data_nasc);
EMAIL(<u>Email, Matricula_FK</u>);
EMPRESTIMO(<u>Codigo</u>, Prazo_devolucao, Matricula_FK);
CONTEM(<u>Codigo_Emprestimo_FK, ISBN_FK</u>)
LIVRO(Titulo, Autor, <u>ISBN</u>, Ano, CNPJ_editora_FK, Codigo_sessao_FK);
SESSAO(<u>Codigo</u>, Localizacao);
EDITORA(<u>CNPJ</u>, Nome);
UNIVERSITARIA(<u>CNPJ_Editora_FK</u>, Cod_regimento);
PARTICULAR(<u>CNPJ_Editora_FK</u>, Tipo_tributacao);


----
# ==RESUMO PRA AVALIAÇÃO==

# regras
- todo atributo composto deve ser dividido em partes simples (atômicas)
- toda relação precisa obrigatoriamente de uma PK
- a PK jamais pode ser nula
- em relacionamentos 1:n recebe os atributos simples e a FK o lado N
- em relacionamentos 1:1 rece os atributos e a FK a relação com participação total
- em relacionamentos m:n, cria-se uma nova relação que recebe as FK das entidades da relação e a PK é a junção das suas FK, tipo assim: PK(<U>FK1 , FK2</U>)
- no mapeamento de entidades fortes cria-se uma relação e a ela é atribuída todos os atributos simples
- para entidades fracas cria-se um relação que tem como PK a junção da sua prórpria chave identificadora com a PK da entidade forte, já a FK é vindo da entidade forte o seu atributo identificador e se mantém com os atributos simples
-  em atributos multivalorados, cria-se uma nova relação que recebe a FK normal, mas sua PK é composta pela PK da relação e seu nome (do próprio atributo multivalorado)

# terminologia
- relação é a tabela, as entidades
- coluna, são os atributos
- tuplas são as linhas
- toda tupla possui uma PK
- definição de relação: é um conjunto de tuplas
- aridade ou grau é a quantidade de atributos (colunas) de uma relação
- cardinalidade é o número de tuplas (linhas) de uma relação
- domínio é o conjunto de valores válidos e atômicos que um atributo pode receber
- estado ou Instância da Relação, são os dados reais populados lá dentro (conjunto dos valores dentro dos atributo)
- intenção da relação é o formato relação(A1, A2, .., An)
- extensão da relação é a mesma coisa da instancia, são as tuplas, ('João', 'M', 202302, 22)


- - - 

- especialização exclusiva criamos uma ÚNICA tabela com todos os atributos das tabelas e a adição do atributo tipo
- especialização de sobreposição 
- especialização total, quando toda pessoa é aluno ou professor, por exemplo, nos deixamos a penas as SUBclasses e apagando a super classe e distribuímos seu astributos para as sub

- **Especialização Exclusiva (Disjunta):** Exatamente o que você disse! Cria-se uma ÚNICA tabela com todos os atributos + a adição do atributo **"tipo"** para saber quem é quem.

- **Especialização de Sobreposição:** Cria-se uma ÚNICA tabela com todos os atributos + a adição de colunas **Booleanas (Verdadeiro/Falso)** para cada papel (ex: _é_aluno_, _é_professor_), já que a pessoa pode ser os dois ao mesmo tempo.

- **Especialização Total:** Na mosca! Apaga a superclasse (pai), deixa APENAS as subclasses e distribui os atributos do pai para dentro delas.

- **Especialização Padrão (Tabelas separadas):** Mantém tudo! Cria uma tabela para o Pai e uma para cada Filho. A chave primária do Pai desce para os filhos.