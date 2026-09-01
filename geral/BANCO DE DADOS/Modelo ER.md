#### ENTIDADES
-  **Forte**: não tem dependência para existir
-  **Fraca**: depende de outra entidade para existir (retângulo com moldura)

#### RELACIONAMENTOS
-  **Binário**: une 2 entidades
-  **Ternário**: une 3 entidades
-  **n-ário**: une N unidades

#### ATRIBUTOS
**$-$ Caracterizam ou descrevem uma entidade ou relacionameto**
- **Simples**: não são divisíveis em unidades mais simples (EX: sexo, CPF, matrícula)
-  **Compostos**: agregação de atributos relacionados, podendo ser divididos em abtibutos simples (EX: endereço -> cep, estado, país)
-  **Multivalorado**: atributo simples que pode ter múltiplos valores (duas bolinhas um dentro da outra)
-  **Derivados**: Podem ser determindos a partir de outros atributos ou entidades (EX: data de nascimento, idade)
-  **Identificador**: é um atributo cujo os valores são distintos de (bolinha preenchida)(EX: ID)
-  **Atributos do Relacionamento**: definido apenas pela existência do relacionamento
eu não aguento mais, a tela fria desse celular, só versua ft não vai meesquentar... amar vc de longe é tão ruim, te quero ao vivo e a cores aqui

### CHAVE CANDIDATA
São todos os atributos possíveis para identificar de forma única uma entidade.
###  CHAVE PRIMÁRIA
Toda chave primária é uma chave candidata, já que a primária é a candidata oficial selecionada pelo projetista.
### REGRAS DE NEGÓCIO
Definem limites e condições para a existência dos dados 
### **RESTRIÇÕES DE INTEGRIDADE**
Restrições nas quais os relacionamentos entre as entidades são submetidos(regras de negócio). São regras aplicadas a colunas de uma tabela, definindo limites para os dados que podem ser inseridos nessas colunas. **Estas regras garantem a integridade e consistência dos dados, evitando erros, duplicações e inconsistências.**

- EX: toda multa de trânsito deve estar associada a um veículo, o salário do empregado deve ser menos que o do gerente

### **CARDINALIDADE**
É o número de instâncias ou ocorrências de cada entidade que podem estar associadas através do relacionamento. Normalmente são quantificadas um número máximo e mínimo de ocorrências

- **Um pra Um (1:1)**: Uma intância de uma entidade A está associada no máximo a uma instância da entidade B, e vice-versa. (EX: um curso é cordenado por no máximo um cordenador)

- **Um pra N (1:N)**: Uma instância de uma entidade A está associada a qualquer número de instâncias na entidade B. Porém, uma instância da entidade B está associada a no máximo uma instância da entidade A. (EX: um professor pode ministrar várias disciplinas, mas uma disciplina só pode ser ministrada por um professor)
![[Captura de tela de 2026-08-06 11-45-51.png|591]]
- **N pra Um (N:1)**: 
- **Muitos pra Muitos (N:N)**:


**LIMITES MÍNIMOS E LIMITES MÁXIMOS** par ordenado de (min,max)
Um professor pode ministrar de 1 a 4 disciplinas e uma disciplina pode ser ministrada por apenas 1 professor. Veja:

![[Captura de tela de 2026-08-06 11-55-15.png|480]]


### **PARTICIPAÇÃO**
Define a existência de uma entidade através do relacionamento




