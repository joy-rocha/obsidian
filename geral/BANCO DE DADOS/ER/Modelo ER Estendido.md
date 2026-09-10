
## SuperClasse/ SubClasse
*subclasses NÃO precisam de atributo identificador, pois elas ja herdam da super*

## ESPECIALIZAÇÃO 

>É o processo de criar a partir de entidades mais genéricas (SuperClasse), novas entidades mais específicas (SubClasse). O conjunto da SubClasse é formado baseado em alguma característica que distingua as entidades entre si. E permite a herança de propriedades, ouseja, a SubClasse herda os atributos da SuperClasse.

RELACIONAMENTOS DO TIPO ***É UM***:
![[Pasted image 20260813113436.png|283]]


## GERERALIZAÇÃO

>É a união do conteúdo de duas ou mais subentidades (subclasses), formando uma superentidade (superclasse), ou seja, cria, a partir de entidades mais específicas, uma entidade mais ge nérica.

![[Pasted image 20260813145739.png|268]]

## COBERTURA ou HERANÇA de propriedade:

>Nos mecanismos de Generalização e Especialização utiliza-se regras de negócio que representam condições envolvendo a especialização. Essas condições são chamadas de **cobertura** ou **herança** de propriedades. Ela é representada do lado da seta que indica a especialização/generalização por um par de valores (X,Y) onde X representa o conteúdo e Y representa a cobertura.
	$->$ resumindo: **(conteúdo, cobertura)**   
	 $->$ ou seja :**(exclusiva/sobreposição, total/parcial)**


## GENERALIZAÇÃO / ESPECIALIZAÇÃO, tipos de cobertura:
$-$ ***TOTAL ($T$) :*** 
**Obrigatório** - Todo registro da entidade "Pai" **TEM** que se encaixar em uma entidade "Filha".

$-$ ***PARCIAL ($P$) :*** 
**Opcional** - O registro da entidade "Pai" **PODE** ter uma entidade "Filha", mas não é obrigado.

$-$ ***EXCLUSIVA ($E$):***
Um registro da super-classe pode pertencer a **no máximo UMA** sub-classe. As opções são mutuamente exclusivas (**ou é uma coisa, ou é outra**).

$-$ ***SOBREPOSIÇÃO ($S$):***
Um registro da super-classe pode pertencer a **MÚLTIPLAS** sub-classes simultaneamente. A entidade "compartilha" papéis. (**várias coisas ao mesmo tempo**)

| ***especialização PARCIAL***              | ***especialização TOTAL***                |
| ----------------------------------------- | ----------------------------------------- |
| ![[Pasted image 20260813145519.png\|299]] | ![[Pasted image 20260813150046.png\|301]] |

| ***especialização EXCLUSIVA***       | ***especialização SOBREPOSIÇÃO***    |
| ------------------------------------ | ------------------------------------ |
| ![[Pasted image 20260813151155.png]] | ![[Pasted image 20260813152122.png]] |


## AGREGAÇÃO -> Entidade Associativa

>**Agregação** é a abstração/conceito de **"empacotar"** um relacionamento existente (junto com as entidades que participam dele) e fingir que esse bloco inteiro é uma **única grande entidade**. (**Ela permite que a relação entre $A$ e $B$ se conecte com $C$**) .
>
>**CONDIÇÃO**: Só pode ocorrer quando há relacionamentos de **muitos pra muitos**, cardinalidade $(n,n)$

#### **ENTIDADE ASSOCIATIVA** 

>É uma **Entidade de verdade** que surgiu a partir de um relacionamento (geralmente um relacionamento $N:N$ que precisava ter atributos próprios ou fazer novos relacionamentos).

- **O foco da Entidade Associativa é a estrutura**: ela possui atributos próprios, pode ter sua própria chave primária e vira uma **tabela física** quando você passa o modelo para o banco de dados SQL.

![[Pasted image 20260813154613.png|592]]