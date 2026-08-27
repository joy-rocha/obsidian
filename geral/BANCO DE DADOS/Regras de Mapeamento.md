#### Entidades regurales ou fortes 
-  Para cada entidade regular $E$ noesquema ER, cria-se uma relação $R$ que inclui os atributos simples de $E$
-  Para cada atributo composto cria-se atributos mais sinmples, ele meio que separa em partes menores, **decomposmos em atributos simples**
- Atributos múltivalorados não são representados, pois um atributo tem que ser atômico 
#### Entidades fracas
-  Para cada entidade fraca $E$, cria-se uma relação $R$ e incluí-se todos os atributos simples de $E$
-  E acrescentamos um atributo $\text{chave estrangeira}$ em $R$, que vai ser a  mesma $\text{chave primária}$ de $E$
-  Foren key - bote _fk depois do nome da chave estrangeira para representá-la
-  A Chave Primária de uma relação FRACA é uma chave composta, que é a primária e a estrangeira separada por vírgula e ambas sublinhadas
#### Relacionamentos 1:1 
-  Identifica-se as relações $S$ e $T$ que correspondem às ebtidades que participam do relacionamento
-  Para ser $S$ vc escolhe a entidade que tem participação total no relacionamento
-  Inclui todos os atributos dos relacionamento como atributos de $S$
-  
#### Relacionamentos 1:n
#### Relacionamentos n:1
#### Relacionamentos n:n