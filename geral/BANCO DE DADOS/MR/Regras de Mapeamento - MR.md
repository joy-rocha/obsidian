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


