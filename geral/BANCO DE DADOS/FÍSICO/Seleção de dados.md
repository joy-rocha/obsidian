
==🔵**SELECT** "função embutida" **FROM** "tabela" **WHERE** "condição"==
```c
SELECT SUN(salário) FROM empregado;
SELECT MIN(data_nasc) FROM empregado WHERE lsexo = 'M';
SELECT COUNT(*) FROM empregado;
SELECT AVG(salário) FROM empregado WHERE dpto = 125;
SELECT dpto, COUNT(*), AVG(salário) FROM empregado GROUP BY dpto;
//para cada departamento selecione o codigo do departamento, a quantidade de empregados e a média salarial
SELECT dpto, COUNT(*) AS "Total de empregados", AVG(salário) FROM empregado GROUP BY dpto;
SELECT sexo, COUNT(*) FROM empregado GROUP BY sexo;
//mostra quantas pessoas tem em cada categoria do sexo ('M' e 'F')
SELECT dpto, salário FROM empregado GROUP BY dpto HAVING SUN(salário)>1000;
//para cada departamento que a soma dos salários pagos é maior que 1000 liste o departamento e o valor pago.
SELECT nome FROM empregado WHERE salario BETWEEN 1000 AND 2000;
SELECT * FROM empregado WHERE data_nasc BETWEEN (19700101 AND 19791231);
//mostra as pessoas que nasceram na década de 70
SELECT matrícula, nome FROM empregado WHERE nome LIKE '%Silva';
//ve se o ultimo nome do meu empregado termina com silva
SELECT código,nome FROM empregado WHERE codigo NOT LIKE '_A%'
//exibir codigo e nomes de todos os empregados, exceto os que tem 'A' no segundo caractere

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
- ***HAVING*** - só é usada junto com o GROUP BY, essa função serve pra dizer um limite de agrupamento, por exemplo agrupe os salários maiores que 1000, é uma condição para que o agrupamento aconteça.
- ATENÇÃO: *o HAVING so serve pra funções de agregação (COUNT, SUM, AVG)*

### **Operadores de Comparação**
- **BETWEEN** - entre
- **LIKE** - é o 'como' ou 'semelhante a'. É usado em strings e tem o carácter % e o __ reservados. Onde o % substitui um intervalo, já o __ substitui um só caractere
-  **YEAR, MONTH, DAY** - seleciona apenas o ano, mês ou dia de uma data 

### **Operadores Aritiméticos**
### **+, -, \*, /**

