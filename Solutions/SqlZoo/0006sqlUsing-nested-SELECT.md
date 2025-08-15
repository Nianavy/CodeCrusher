# Using nested SELECT

```SQL
SELECT name FROM world WHERE continent = 
(SELECT continent 
FROM world WHERE name = 'Brazil')

-- alias(some dbengine)
SELECT name FROM world WHERE continent = 
  (SELECT continent FROM world WHERE name='Brazil') AS brazil_continent
  
  
SELECT name, continent FROM world
WHERE continent IN
  (SELECT continent 
     FROM world WHERE name='Brazil'
                   OR name='Mexico')
                   
                   
                   
SELECT
 population/(SELECT population FROM world
             WHERE name='United Kingdom')
  FROM world
WHERE name = 'China'





-- Using ALL or ANY when the right of compare operator' value over than one
SELECT name FROM world
 WHERE population > ALL
      (SELECT population FROM world
        WHERE continent='Europe')

```



| name國家名  | continent洲份 | area面積 | population人口 | gdp國民生產總值 |
| :---------- | :------------ | :------- | :------------- | :-------------- |
| Afghanistan | Asia          | 652230   | 25500100       | 20343000000     |
| Albania     | Europe        | 28748    | 2831741        | 12960000000     |
| Algeria     | Africa        | 2381741  | 37100000       | 188681000000    |
| Andorra     | Europe        | 468      | 78115          | 3712000000      |
| Angola      | Africa        | 1246700  | 20609294       | 100990000000    |
| ...         |               |          |                |                 |

```SQL
SELECT name FROM world
  WHERE population >
     ANY (SELECT population FROM world
      WHERE name='Russia');


SELECT name FROM world
  WHERE continent = 'Europe' AND gdp/population >
     ANY (SELECT gdp/population FROM world
      WHERE name='United Kingdom');


SELECT name, continent 
FROM world
WHERE continent IN (
    SELECT continent 
    FROM world 
    WHERE name IN ('Argentina', 'Australia')
)
ORDER BY name;


SELECT name, population FROM world 
WHERE population > 
(SELECT population FROM world WHERE name = 'Canada')
AND population <
(SELECT population FROM world WHERE name = 'Poland');


SELECT name, 
CONCAT(ROUND(100*population/(SELECT population FROM world WHERE name = 'Germany')),'%') 
FROM world WHERE continent = 'Europe';


SELECT name
FROM world
WHERE continent != 'Europe' 
AND gdp >= ALL(SELECT gdp 
               FROM world 
               WHERE continent = 'Europe' 
               AND gdp>0);


SELECT continent, name, area
FROM world x
WHERE area = (
    SELECT MAX(area)
    FROM world y
    WHERE y.continent = x.continent
      AND area IS NOT NULL
);


SELECT continent, name
FROM world x
WHERE name = (
    SELECT name
    FROM world y
    WHERE y.continent = x.continent
    ORDER BY name ASC
    LIMIT 1
);


SELECT w1.name, w1.continent, w1.population
FROM world w1
WHERE w1.continent IN (
    SELECT w2.continent
    FROM world w2
    GROUP BY w2.continent
    HAVING MAX(w2.population) <= 25000000
)
ORDER BY w1.continent, w1.name;


SELECT name, continent
FROM world x
WHERE population/3 >= ALL(SELECT population
                        FROM world y
                        WHERE y.continent=x.continent
                        AND population>0
                        AND y.name != x.name);
```





above content from

[Using nested SELECT/zh - SQLZoo](https://www.sqlzoo.net/wiki/Using_nested_SELECT/zh)

[SELECT within SELECT Tutorial/zh - SQLZoo](https://www.sqlzoo.net/wiki/SELECT_within_SELECT_Tutorial/zh)

