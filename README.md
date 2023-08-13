#Estudos de SQL:
Existem diversos conceitos que, conjuntamente, compreendem um “ambiente SQL”:

• Esquemas </b>
• Catálogos 
• Domínios 
• Conexões e Sessões 
• Usuários 
• Privilégios 
• Visões 
• Transações 
• etc


SQL Lição 1: Consultas SELECT:

É dada uma tabela de dados, a consulta mais básica que poderíamos escrever seria aquela que seleciona algumas colunas (propriedades) da tabela com todas as linhas (instâncias).

```
SELECT coluna, outra_coluna
FROM minhatabela; 
```

O resultado desta consulta será um conjunto bidimensional de linhas e colunas, mas apenas com as colunas que solicitamos.

Para que seja todas as colunas de dados de uma tabela, podemos usar  `*` no lugar de listar todos os nomes de colunas individualmente.

```
SELECT * FROM minhatabela;
```

SQL Lição 2: Consultas com restrições (Pt. 1):

Para filtrar o retorno de determinados resultados, precisamos usar uma `WHERE` cláusula na consulta. A cláusula é aplicada a cada linha de dados verificando os valores específicos da coluna para determinar se ela deve ser incluída nos resultados ou não.

```
SELECT coluna, outra_coluna
FROM minhatabela
WHERE condição 
AND/OR outra_condição;
```
SQL Lição 3: Consultas com restrições (Pt. 2):

Mostramos alguns operadores específicos de dados de texto comuns abaixo:

|   |   |   |
|---|---|---|
|Operator|Condition|Example|
|=|Case sensitive exact string comparison (_notice the single equals_)|col_name = "abc"|
|!= or <>|Case sensitive exact string inequality comparison|col_name != "abcd"|
|LIKE|Case insensitive exact string comparison|col_name LIKE "ABC"|
|NOT LIKE|Case insensitive exact string inequality comparison|col_name NOT LIKE "ABCD"|
|%|Used anywhere in a string to match a sequence of zero or more characters (only with LIKE or NOT LIKE)|col_name LIKE "%AT%"  <br>(matches "AT", "ATTIC", "CAT" or even "BATS")|
|_ | Used anywhere in a string to match a single character (only with LIKE or NOT LIKE)|col_name LIKE "AN_"  <br>(matches "AND", but not "AN")|
|IN (…)|String exists in a list|col_name IN ("A", "B", "C")|
|NOT IN (…)|String does not exist in a list|col_name NOT IN ("D", "E", "F")|

Alguns exemplos: 
1. Encontre todos os filmes dirigidos por John Lasseter:

```
SELECT Director, title FROM movies
WHERE Director LIKE 'John Lasseter
```

SQL Lição 4: Filtrando e classificando os resultados da consulta:

SQL fornece uma maneira conveniente de descartar linhas que possuem um valor de coluna duplicado usando a `DISTINCT` palavra-chave.

```
SELECT DISTINCT coluna, outra_coluna
FROM minhatabela
WHERE condição(s);
```

Como a `DISTINCT` palavra-chave removerá cegamente linhas duplicadas, aprenderemos descartar duplicatas com base em colunas específicas usando o agrupamento: `GROUP BY`. 

Exemplo:

1. Liste todos os diretores de filmes da Pixar (em ordem alfabética), sem duplicatas:
 ```
 SELECT DISTINCT director
 FROM movies
 ORDER BY director;
 ```


o SQL permite que determinada coluna seja ordenada de forma crescente ou decrescente usando a ORDER BY.

**ORDENAR RESULTADOS:**
```
SELECT coluna, outra_coluna 
FROM minhatabela WHERE condições 
ORDER BY coluna ASC/DESC;
```

**LIMITANDO OS RESULTADOS A UM SUBCONJUNTO:**

```
SELECT coluna, outra_coluna
FROM minhatabela
WHERE condição (oes)
ORDER BY coluna ASC/DESC
LIMIT num_limite OFFSET num_offset;
```

O LIMIT reduzirá o número de linhas a serem retornadas;
OFFSET especificará de onde começar a contar o número de linhas.

Exemplo:

1. Liste os últimos quatro filmes da Pixar lançados (ordenados do mais recente ao menos):
 ```
SELECT title, year
FROM movies
ORDER BY year DESC
LIMIT 4;
 ```

2. Liste os **próximos** cinco filmes da Pixar em ordem alfabética:

```
SELECT title
FROM movies
ORDER BY TITLE ASC
LIMIT 5 OFFSET 5;
 ```

Exercícios gerias: 

1. Liste todas as cidades canadenses e suas populações:
```
SELECT City, Country, Population
FROM north_american_cities
WHERE Country = 'Canada';
```

2. Ordene todas as cidades dos Estados Unidos por sua latitude de norte a sul:
```
SELECT Country, City, Latitude, Longitude 
FROM north_american_cities
WHERE Country = 'United States'
ORDER BY latitude DESC
```

3. Liste todas as cidades a oeste de Chicago, ordenadas de oeste para leste:
```
SELECT Country, City, Longitude
FROM north_american_cities
WHERE Longitude < (SELECT Longitude FROM north_american_cities WHERE City = 'Chicago')
ORDER BY Longitude ASC;
```

4. Liste as duas maiores cidades do México (por população):
```
SELECT Country, City, Population
FROM north_american_cities
WHERE Country = 'Mexico' 
ORDER BY Population DESC
LIMIT 2;
```

5. Liste a terceira e quarta maiores cidades (por população) nos Estados Unidos e sua população:
```
SELECT Country, City, Population
FROM north_american_cities
WHERE Country = 'United States' 
ORDER BY Population DESC
LIMIT 2 OFFSET 2;
```

**SQL Lição 6: Consultas de várias tabelas com JOINs:**

Usando o JOIN em uma consulta podemos combinar dados de linha em duas tabelas separadas usando essa chave exclusiva ( *Chave primária* ). 

INNER JOIN: 

```
SELECT coluna, outra_coluna
FROM minhatabela
INNER JOIN outra_coluna 
ON minha_tabela.id = outra_tabela.id
WHERE condição
ORDER BY coluna ASC/DESC
LIMIT num_limite OFFSET num_offset;
```

O `INNER JOIN` é um processo que combina linhas da primeira tabela e da segunda tabela que possuem a mesma chave (conforme definido pela `ON`restrição) para criar uma linha de resultado com as colunas combinadas de ambas as tabelas. Depois que as tabelas são unidas, as outras cláusulas que aprendemos anteriormente são aplicadas.

1. Mostre os números de vendas de cada filme que teve melhor desempenho internacional em vez de doméstico:
```
SELECT Domestic_sales, International_sales, Title
FROM Movies
INNER JOIN Boxoffice
ON Movies.id = Boxoffice.Movie_id
WHERE International_sales > Domestic_sales
ORDER BY Domestic_sales DESC
```

**SQL Lição 7: OUTER JOINs:**

Se as duas tabelas tiverem dados assimétricos, o que pode acontecer facilmente quando os dados são inseridos em etapas diferentes, teríamos que usar um `LEFT JOIN`ou `RIGHT JOIN` ou então `FULL JOIN` para garantir que os dados que precisamos não sejam deixados de fora dos resultados.

```
SELECT coluna, outra_coluna
FROM minha_tabela
INNER/LEFT/RIGHT/FULL JOIN outra_tabela
ON minha_tabela.id = outra_tabela.outro_id
WHERE condição
ORDER BY coluna, ASC/DESC
LIMIT num_limite OFFSET num_offset;
```

Ao unir a tabela A à tabela B, a `LEFT JOIN` simplesmente inclui linhas de A independentemente de uma linha correspondente ser encontrada em B. O `RIGHT JOIN` é o mesmo, mas invertido, mantendo as linhas em B independentemente de uma correspondência ser encontrada em A. Finalmente, um `FULL JOIN`simplesmente significa que as linhas de ambas as tabelas são mantidas, independentemente de existir uma linha correspondente na outra tabela.

1. Encontre a lista de todos os prédios que possuem funcionários:

```
SELECT DISTINCT Building_name
FROM Buildings 
INNER JOIN Employees ON Building_name = Building;
```

2. Liste todos os prédios e as funções distintas dos funcionários em cada prédio (incluindo prédios vazios):
```
SELECT DISTINCT building_name, role 
FROM buildings 
LEFT JOIN employees ON building_name = building;
```

**SQL Lição 8: Uma breve observação sobre NULLs:**

Uma alternativa aos valores  `NULL` em seu banco de dados é ter valores _padrão apropriados para o tipo de dados_ , como 0 para dados numéricos, strings vazias para dados de texto etc. `NULL`distorcerá a análise posterior (por exemplo, ao obter médias de dados numéricos).

Às vezes, também não é possível evitar valores `NULL`. Nesses casos, você pode testar uma coluna em busca de `NULL` em uma `WHERE` cláusula usando a restrição `IS NULL` ou `IS NOT NULL`.
```
SELECT coluna, outra_coluna
FROM minha_tabela
WHERE coluna IS/IS NOT NULL
AND/OR outra_condição
```

1. Encontre o nome e a função de todos os funcionários que não foram atribuídos a um edifício: 
```
SELECT Role, Name, Building
FROM Employees
WHERE Building is NULL
```

2. Encontre os nomes dos prédios que não possuem funcionários:
```
SELECT DISTINCT building_name
FROM buildings 
LEFT JOIN employees 
ON building_name = building
WHERE role IS NULL;
```

**SQL Lição 9: Consultas com expressões:**

Também é possível usar *expressões* para escrever uma lógica mais complexa em valores de coluna em uma consulta. Essas expressões podem usar funções matemáticas e de string junto com aritmética básica para transformar valores quando a consulta é executada.

Exemplo de física:
```
SELECT particle_speed / 2.0 AS half_particle_speed FROM physics_data WHERE ABS(particle_position) * 10.0 > 500;
```

Quando forem usadas expressões na parte da consulta, elas também  `SELECT`  recebam um _alias_ descritivo usando a `AS` palavra-chave.

```
SELECT _col_expression_ AS _expr_description_, … 
FROM mytable;
```

Além de expressões, colunas regulares e até tabelas também podem ter aliases para facilitar a referência na saída e como parte da simplificação de consultas mais complexas.
```
SELECT column AS better_column_name, … FROM a_long_widgets_table_name AS mywidgets INNER JOIN widget_sales ON mywidgets.id = widget_sales.widget_id;
```

Exemplos:

1. Liste todos os filmes e suas vendas combinadas em **milhões** de dólares:
```
SELECT title, 
(domestic_sales + international_sales) / 1000000 AS gross_sales_millions
FROM movies
  JOIN boxoffice
    ON movies.id = boxoffice.movie_id;
```

2. Liste todos os filmes e suas classificações **em porcentagem**:
```
SELECT title, rating * 10 AS rating_percentagem
FROM movies
  JOIN boxoffice
    ON movies.id = boxoffice.movie_id;
```

3. Liste todos os filmes que foram lançados em anos pares:
```
SELECT title, year
FROM movies
WHERE year % 2 = 0;
```

**SQL Lição 10: Consultas com agregados (Pt. 1):**

|   |   |
|---|---|
|Função|Descrição|
|**COUNT(** * **)** , **COUNT(** coluna **)**|Uma função comum usada para contar o número de linhas no grupo se nenhum nome de coluna for especificado. Caso contrário, conte o número de linhas no grupo com valores não NULL na coluna especificada.|
|**MIN(** coluna **)**|Localiza o menor valor numérico na coluna especificada para todas as linhas do grupo.|
|**MAX(** coluna **)**|Localiza o maior valor numérico na coluna especificada para todas as linhas do grupo.|
|**AVG(** coluna )|Localiza o valor numérico médio na coluna especificada para todas as linhas do grupo.|
|**SOMA(** coluna **)**|Localiza a soma de todos os valores numéricos na coluna especificada para as linhas do grupo.|

```
SELECT AGG_FUNC(_column_or_expression_) AS aggregate_description
FROM minha_tabela WHERE _constraint_expression_ GROUP BY coluna;
```

1. Encontre o tempo mais longo que um funcionário esteve no estúdio: 
```
SELECT MAX(Years_employed) FROM employees;
```

2. Para cada função, encontre o número médio de anos empregados pelos funcionários nessa função: 
```
SELECT Role, AVG(Years_employed) AS Media
FROM employees
GROUP BY Role;
```

3. Encontre o número total de anos de funcionários trabalhados em cada prédio:
```
SELECT 	Building, SUM(Years_employed) AS Total
FROM employees
GROUP BY Building;
```

**SQL Lição 11: Consultas com agregados (Pt. 2):**

Felizmente, o SQL nos permite fazer isso adicionando uma `HAVING`(cláusula adicional) que é usada especificamente com a `GROUP BY` para nos permitir filtrar linhas agrupadas do conjunto de resultados.

```
SELECT agrupar_por_coluna, AGG_FUNC(coluna_expressão) 
AS resultado_agregado FROM minha_tabela
WHERE condicao GROUP BY coluna
HAVING agrupar_condicao;
```

As HAVING são escritas da mesma forma que as WHERE e são aplicadas ás linhas agrupadas.

1. Encontre o número de Artistas no estúdio (sem a cláusula **HAVING**):
```
SELECT 	role, COUNT(*) AS Total_de_Artista
FROM employees WHERE role = "Artist";
```

> `COUNT(*)` é uma função de agregação que conta o número total de registros que atendem às condições da consulta.

> `WHERE role = "Artist"` : Essa cláusula filtra os resultados para incluir apenas os registros em que a coluna "role" seja igual a "Artist". Isso significa que estamos interessados apenas nos funcionários que possuem a função de "Artista".

2. Encontre o número de funcionários de cada função no estúdio:
```
SELECT Role, COUNT(*)
FROM Employees
GROUP BY role;
```

3. Encontre o número total de anos empregados por todos os engenheiros:
```
SELECT 	role, Sum(Years_employed)
FROM employees WHERE role = "Engineer";
```

**SQL Lição 12: Ordem de execução de uma Consulta:**

Consulta SELECT completa:
```
SELECT DISTINCT coluna, AGG_FUNC(coluna ou expressão)
FROM minha_tabela
	JOIN outra_tabela
		ON minha_tabela.coluna = outra_tabela.coluna
	WHERE expressão_restrissão
	GROUP BY coluna
	HAVING expressão_restrissão
	ORDER BY coluna ASC/DESC
	LIMIT count OFFSET COUNT;
```

Cada consulta começa com a localização dos dados de que precisamos em um banco de dados e, em seguida, filtrando esses dados em algo que possa ser processado e compreendido o mais rápido possível. Como cada parte da consulta é executada sequencialmente, é importante entender a ordem de execução para saber quais resultados estão acessíveis e onde.

# Ordem de execução da consulta:

1. **FROM e JOIN**
    São executadas primeiro para determinar o conjunto de dados de trabalho.
    A cláusula `FROM` é usada para especificar a tabela ou tabelas das quais você deseja selecionar os dados.
	A cláusula `JOIN` é usada para combinar dados de duas ou mais tabelas relacionadas em uma única consulta.

2. **WHERE**
	Filtra os resultados de acordo com as condições especificadas.

3. **GROUP BY**
	 É usada junto com as funções de agregação, para agrupar os resultados com base em valores específicos de uma ou mais colunas.

4. **HAVING**
	É usada para filtrar os resultados de grupos de registros criados pelo GROUP BY  e agregados por funções de agregação. 
	
5. **SELECT**
	É usada para recuperar dados de uma ou mais tabela.

6. **DISTINC**
	As linhas com valores duplicados na coluna marcada como DISTINC serão descartadas.

7. **ORDER BY**
	Se uma ordem for especificada pela ORDER BY, as linhas serão classificadas pelos dados especificados em ordem crescente ou decrescente. 

8. **LIMIT / OFFSET**
	As linhas que estiverem fora do intervalo especificado pelo `LIMIT`e `OFFSET`são descartadas, deixando o conjunto final de linhas a ser retornado da consulta.

**Questões:**
1. Encontre o número de filmes que cada diretor dirigiu:
```
SELECT Director, COUNT (*) As Diretor_filme
FROM Movies GROUP BY Director; 
```

2. Encontre o total de vendas domésticas e internacionais que podem ser atribuídas a cada diretor:
```
SELECT Director, SUM(Domestic_sales + International_sales) As Vendas_Diretor
FROM Movies
INNER JOIN Boxoffice ON Movies.Id = Boxoffice.Movie_id
GROUP BY Director;
```

SQL Lição 13: Inserindo linhas:

É possível inserir dados usando `INSERT`

Inserir declaração com valores para todas as colunas

```
INSERT INTO minha_tabela VALUES (value_or_expr, another_value_or_expr, …), (value_or_expr_2, another_value_or_expr_2, …), …;
```
`
Inserir declaração com colunas específicas:
```
INSERT INTO minha_tabela (coluna, outra_coluna)
VALUES (valor_or_expr, outro_valor_or_expr, …), (valor_or_expr_2, outro_valor_or_expr_2, …);
```

Exemplo Instrução de inserção com expressões:

```
INSERT INTO boxoffice (movie_id, rating, sales_in_millions) VALUES (1, 9.9, 283742034 / 1000000);
```

1. Adicione a nova produção do estúdio, **Toy Story 4** à lista de filmes (você pode usar qualquer diretor): 
```
INSERT INTO Movies (Id, Title, Director, Year, Length_minutes)
VALUES (4, 'Toy Story 4', 'John Lasseter', 2022, 85);
```

2. Toy Story 4 foi lançado com aclamação da crítica! Teve uma classificação de **8,7** , e fez **340 milhões no mercado interno** e **270 milhões internacionalmente** . Adicione o registro à `BoxOffice` tabela:
```
INSERT INTO  Boxoffice  (Movie_id,	Rating,	Domestic_sales,	International_sales)
VALUES (4, 87, 340.000, 340.000);
```

**SQL Lição 14: Atualizando linhas:**

Atualizar os dados existentes por meio de `UPDATE`

```
UPDATE minha_tabela
SET coluna = Valor_or_expr
...
WHERE condição;
```
A instrução funciona tomando vários pares de coluna/valor e aplicando essas alterações a cada linha que satisfaça a restrição na `WHERE`. 

1. O diretor de A Bug's Life está incorreto, na verdade foi dirigido por **John Lasseter**: 

```
UPDATE Movies
SET Director = 'John Lasseter'
WHERE Title = 'A Bug's Life';
```

**SQL Lição 15: Excluindo linhas:**

Para deletar dados de uma tabela no banco de dados, pode usar `DELETE` por meio da `WHERE` 

```
DELETE FROM minha_tabela
WHERE condição;
```

