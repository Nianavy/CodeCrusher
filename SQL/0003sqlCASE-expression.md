# CASE expression

| name        | continent | area    | population | gdp          |
| :---------- | :-------- | :------ | :--------- | :----------- |
| Afghanistan | Asia      | 652230  | 25500100   | 20343000000  |
| Albania     | Europe    | 28748   | 2831741    | 12960000000  |
| Algeria     | Africa    | 2381741 | 37100000   | 188681000000 |
| Andorra     | Europe    | 468     | 78115      | 3712000000   |
| Angola      | Africa    | 1246700 | 20609294   | 100990000000 |
| ...         |           |         |            |              |



```SQL
SELECT name, continent, population FROM world;

SELECT name FROM world WHERE population > 200000000;

SELECT name, gdp/population FROM world WHERE population > 200000000;

SELECT name, population/1000000 FROM world WHERE continent = 'South America';

SELECT name, population FROM world WHERE name IN ('France', 'Germany', 'Italy');

SELECT name FROM world WHERE name LIKE '%United%';

SELECT name, population, area FROM world WHERE area > 3000000 OR population > 250000000;

SELECT name, population, area FROM world WHERE (area > 3000000 AND population <= 250000000)
												OR (population > 250000000 AND area <= 3000000);
												
SELECT name, ROUND(population/1000000, 2), ROUND(gdp/1000000000, 2) FROM world WHERE continent = 'South America';

SELECT name, ROUND(gdp/population, -3) FROM world WHERE gdp > 1000000000000;

SELECT name, 
       CASE 
           WHEN continent = 'Oceania' AND name LIKE 'N%' THEN 'Australasia'
           ELSE continent 
       END AS continent
FROM world
WHERE name LIKE 'N%';

SELECT name,
       CASE
           WHEN continent IN ('Asia', 'Europe') THEN 'Eurasia'
           WHEN continent IN ('North America', 'South America', 'Caribbean') THEN 'America'
           ELSE continent
       END AS continent
FROM world
WHERE name LIKE 'A%' OR name LIKE 'B%';

SELECT name,
       continent AS original_continent,
       CASE
           WHEN continent = 'Oceania' THEN 'Australasia'
           WHEN continent = 'Eurasia' AND name = 'Turkey' THEN 'Asia'
           WHEN continent = 'Eurasia' THEN 'Europe'
           WHEN continent = 'Caribbean' AND name LIKE 'B%' THEN 'North America'
           WHEN continent = 'Caribbean' THEN 'South America'
           ELSE continent
       END AS new_continent
FROM world;
```



 above context from [SQLZoo:SELECT from WORLD Tutorial/zh - SQLZoo](https://www.sqlzoo.net/wiki/SQLZoo:SELECT_from_WORLD_Tutorial/zh)