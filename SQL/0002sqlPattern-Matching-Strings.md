# Pattern Matching Strings

| name        | continent |
| :---------- | :-------- |
| Afghanistan | Asia      |
| Albania     | Europe    |
| Algeria     | Africa    |
| Andorra     | Europe    |
| Angola      | Africa    |
| ....        |           |



```SQL
SELECT name FROM world WHERE name LIKE 'Y%';

SELECT name FROM world WHERE name LIKE '%Y';

SELECT name FROM world WHERE name LIKE '%x%';

SELECT name FROM world WHERE name LIKE '%land';

SELECT name FROM world WHERE name LIKE 'C%ia';

SELECT name FROM world WHERE name LIKE '%oo%';

SELECT name FROM world WHERE name LIKE '%a%a%a%';

SELECT name FROM world WHERE name LIKE '_t%' ORDER BY name;

SELECT name FROM world WHERE name LIKE '%o__o%';

SELECT name FROM world WHERE name LIKE '____';

SELECT name FROM world WHERE name = capital;

SELECT name FROM world WHERE capital = concat(name, ' City'); 

SELECT capital, name FROM world WHERE capital LIKE concat('%', name, '%');

SELECT name, capital FROM world WHERE capital LIKE concat('%', name, '%') AND name != capital;

SELECT name, REPLACE(capital, name, '') AS ext FROM world WHERE capital LIKE CONCAT(name, '%') AND capital != name;
```



above context from [SELECT names/zh - SQLZoo](https://www.sqlzoo.net/wiki/SELECT_names/zh)

