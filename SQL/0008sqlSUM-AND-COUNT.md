# SUM and COUNT

| name        | continent | area    | population | gdp          |
| :---------- | :-------- | :------ | :--------- | :----------- |
| Afghanistan | Asia      | 652230  | 25500100   | 20343000000  |
| Albania     | Europe    | 28748   | 2831741    | 12960000000  |
| Algeria     | Africa    | 2381741 | 37100000   | 188681000000 |
| Andorra     | Europe    | 468     | 78115      | 3712000000   |
| Angola      | Africa    | 1246700 | 20609294   | 100990000000 |
| ...         |           |         |            |              |



```SQL
SELECT SUM(population)
FROM world;


SELECT DISTINCT continent FROM world;


SELECT SUM(gdp) FROM world WHERE continent = 'Africa';


SELECT COUNT(name) FROM world WHERE area >= 1000000;


SELECT SUM(population) FROM world WHERE name IN ('France','Germany','Spain');


SELECT continent, COUNT(name) AS num_countries
FROM world
GROUP BY continent;


SELECT continent, COUNT(name) AS country_count
FROM world
WHERE population >= 10000000
GROUP BY continent;


SELECT continent
FROM world
GROUP BY continent
HAVING SUM(population) >= 100000000;
```



above content from [SUM and COUNT/zh - SQLZoo](https://www.sqlzoo.net/wiki/SUM_and_COUNT/zh)