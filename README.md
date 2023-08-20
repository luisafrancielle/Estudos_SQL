# Estudos de SQL:

Estudo realizado usando [SQLBOLT](https://sqlbolt.com/) como base.

SQL Lição 1: Consultas SELECT:

A consulta mais básica que poderíamos escrever seria aquela que seleciona algumas colunas (propriedades) da tabela com todas as linhas (instâncias).

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
|Operador|Condição|Exemplo|
|=|Comparação exata de strings com distinção entre maiúsculas e minúsculas ( _observe o único igual_ )|col_name = "abc"|
|!= or <>|Comparação de desigualdade de string exata com distinção entre maiúsculas e minúsculas|col_name != "abcd"|
|LIKE|Comparação de string exata sem distinção entre maiúsculas e minúsculas|col_name LIKE "ABC"|
|NOT LIKE|Case insensitive exact string inequality comparison|col_name NOT LIKE "ABCD"|
|%|Usado em qualquer lugar em uma string para corresponder a uma sequência de zero ou mais caracteres (somente com LIKE ou NOT LIKE)|col_name LIKE "%AT%"  <br>(matches "AT", "ATTIC", "CAT" or even "BATS")|
|_ | Usado em qualquer lugar em uma string para corresponder a um único caractere (somente com LIKE ou NOT LIKE)|col_name LIKE "AN_"  <br>(matches "AND", but not "AN")|
|IN (…)|String existe em uma lista|col_name IN ("A", "B", "C")|
|NOT IN (…)|String não existe em uma lista|col_name NOT IN ("D", "E", "F")|


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

1. Este banco de dados está ficando muito grande, vamos remover todos os filmes lançados **antes de** 2005:
```
DELETE FROM movies
WHERE Year < 2005;
```

**SQL Lição 16: Criando tabelas:**

Para criar uma tabela nova no seu banco: `CREATE TABLE`

Criar instrução de tabela com restrição de tabela opcional e valor padrão: 
```
CREATE TABLE IF NOT EXISTS minha_tabela (
coluna Tipo DEFAULT valor_padrão,
outra_tabela coluna Tipo DEFAULT valor_padrão,
...
);
```

Ao criar uma tabela, pode ser que já exista outra com mesmo nome, para evitar que esse erro aconteça usa se `IF NOT EXISTS`

Podemos encontrar todos os tipos de campos de dados para o postgres neste [link](https://www.postgresql.org/docs/12/datatype.html).

Alguns mais utilizados para números:

	Integer --> Inteiro
	Real ---> Casas decimais 
	Serial --> Auto incrementa o inteiro 
	Numeric --> Definir a precisão das casas decimais

Alguns mais utilizados para textos:

	varchar --> Texto 
	char --> Define a quantidade de caracteres 
	text --> Não tem ideia do tamanho do texto 

Usamos muito o boolean:

	Verdadeiro ou falso

Data e hora:

	date --> data
	time - hora
	timestamp --> Data e hora

RESTRIÇÕES DA TABELA:

|   |   |
|---|---|
|Limitação|Descrição|
|`PRIMARY KEY`|Isso significa que os valores nessa coluna são exclusivos e cada valor pode ser usado para identificar uma única linha nessa tabela.|
|`AUTOINCREMENT`|Para valores inteiros, isso significa que o valor é automaticamente preenchido e incrementado a cada inserção de linha. Não suportado em todos os bancos de dados.|
|`UNIQUE`|Isso significa que os valores nesta coluna devem ser únicos, então você não pode inserir outra linha com o mesmo valor nesta coluna como outra linha na tabela. Difere da `PRIMARY KEY` porque não precisa ser uma chave para uma linha na tabela.|
|`NOT NULL`|Isso significa que o valor inserido não pode ser `NULL`.|
|`CHECK (expression)`|Isso permite que você execute uma expressão mais complexa para testar se os valores inseridos são válidos. Por exemplo, você pode verificar se os valores são positivos ou maiores que um tamanho específico, ou começam com um determinado prefixo, etc.|
|`FOREIGN KEY`|Esta é uma verificação de consistência que garante que cada valor nesta coluna corresponda a outro valor em uma coluna em outra tabela.  <br>  <br>Por exemplo, se houver duas tabelas, uma listando todos os funcionários por ID e outra listando suas informações de folha de pagamento, a 'FOREIGN KEY' pode garantir que cada linha na tabela de folha de pagamento corresponda a um funcionário válido na lista mestre de funcionários.|

Exemplo:
```
CREATE TABLE aluno(
	id serial,
	nome VARCHAR(255),
	cpf CHAR(11),
	observação TEXT,
	idade INTEGER,
	dinheiro NUMERIC(10,2),
	altura real,
	ativo BOOLEAN,
	data_nascimento DATE,
	hora_aula TIME,
	matriculado_em timestamp
);
```

**SQL Lição 17: Alterando tabelas:**

Adicionar, remover ou modificar colunas e restrições em tabela pode ser feito por meio do `ALTER TABLE`

```
ALTER TABLE minha_tabela
ADD coluna Tipo
	DEFAULT valor_padrão;
```

Remover colunas:

```
ALTER TABLE minha_tabela
DROP coluna_que_quer_deletar;
```

Renomear tabela: 

```
ALTER TABLE minha_tabela
RENAME TO novo_nome;
```

1. Adicione uma coluna chamada **Aspect_ratio** com um tipo de dados **FLOAT** para armazenar a proporção em que cada filme foi lançado:
```
ALTER TABLE Movies
ADD Aspect_ratio FLOAT;
```

2. Adicione outra coluna chamada **Language** com um tipo de dados **TEXT** para armazenar o idioma no qual o filme foi lançado. Certifique-se de que o padrão para esse idioma seja **o inglês**:

```
ALTER TABLE Movies
ADD Language TEXT
    DEFAULT English;
```

**SQL Lição 18: Eliminando tabelas**

`DELETE` remove registros individuais de uma tabela, Já o `DROP`remove completamente a tabela. 
```
DROP TABLE IF EXISTS minha_tabela;
```

Se tiver outra tabela dependente de colunas na tabela que está removendo (por exemplo: `FOREIGN KEY`), será necessário atualizar todas as tabelas dependentes primeiro para remover as linhas dependentes ou remover totalmente essas tabelas.

**SUBCONSULTA:**

Dentro de uma `FROM` cláusula, você pode `JOIN` fazer subconsultas com outras tabelas, dentro de uma restrição `WHERE` ou `HAVING`, você pode testar expressões contra os resultados da subconsulta e até mesmo em expressões da `SELECT`cláusula, que permitem retornar dados diretamente da subconsulta.

Como as subconsultas podem ser aninhadas, cada subconsulta deve ser totalmente incluída entre parênteses para estabelecer a hierarquia adequada. As subconsultas podem fazer referência a qualquer tabela no banco de dados e fazer uso das construções de uma consulta normal (embora algumas implementações não permitam que as subconsultas usem `LIMIT` ou `OFFSET`).

Suponha que você tenha uma lista geral de funcionários, seus departamentos (engenharia, vendas etc.), receita e salário. Desta vez, você está examinando toda a empresa para encontrar os funcionários com desempenho abaixo da média em seus departamentos.

Para cada funcionário, você precisaria calcular seu custo relativo à receita média gerada por todas as pessoas em seu departamento. Para tirar a média do departamento, a subconsulta precisará saber em qual departamento cada funcionário está:


```
SELECT * FROM Funcionários
WHERE salário > (SELECT AVG(receita_gerada)
FROM Funcionários AS dept_funcionários
WHERE dept_funcionários.departamento = funcionários.departamento);
```

**Tópico SQL: Uniões, Interseções e Exceções:**

Ao trabalhar com várias tabelas, o operador `UNION` e `UNION ALL`permite anexar os resultados de uma consulta a outra, assumindo que elas tenham a mesma contagem de colunas, ordem e tipo de dados. Se você usar o `UNION` sem o `ALL`, as linhas duplicadas entre as tabelas serão removidas do resultado.

```
SELECT coluna, outra_coluna
FROM minha_tabela
UNION / UNION ALL / INTERSECT / EXCEPT
SELECT outra_coluna, outra_coluna2
FROM outra_tabela
ORDER BY coluna DESC
LIMIT n;
```

