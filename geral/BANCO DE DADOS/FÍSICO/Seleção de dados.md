
==🔵**SELECT** "função embutida" **FROM** "tabela" **WHERE** "condição"==
```c
SELECT SUN(salário) FROM empregado;
SELECT MIN(data_nasc) FROM empregado WHERE lsexo = 'M';
SELECT COUNT(*) FROM empregado;
SELECT AVG(salário) FROM empregado WHERE dpto = 125;
SELECT dpto, COUNT(*), AVG(salário) FROM empregado GROUP BY dpto
//para cada departamento selecione o codigo do departamento, a quantidade de empregados e a média salarial
SELECT dpto, COUNT(*) AS "Total de empregados", AVG(salário) FROM empregado GROUP BY dpto
SELECT sexo, COUNT(*) FROM empregado GROUP BY sexo
//mostra quantas pessoas tem em cada categoria do sexo ('M' e 'F')
SELECT dpto, salário FROM empregado GROUP BY dpto HAVING SUN(salário)>1000
//para cada departamento que a soma dos salários pagos é maior que 1000 liste o departamento e o valor pago.
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
- ***HAVING*** - só é usada junto com o GROUP BY, essa função serve pra dizer um limite de agrupamento, por exemplo agrupe os salários maiores que 1000, é uma condição para que o agrupamento aconteça



