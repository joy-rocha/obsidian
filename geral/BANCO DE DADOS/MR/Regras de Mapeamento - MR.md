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


