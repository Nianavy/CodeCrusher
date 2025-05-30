| name        | continent | area    | population | gdp          |
| :---------- | :-------- | :------ | :--------- | :----------- |
| Afghanistan | Asia      | 652230  | 25500100   | 20343000000  |
| Albania     | Europe    | 28748   | 2831741    | 12960000000  |
| Algeria     | Africa    | 2381741 | 37100000   | 188681000000 |
| Andorra     | Europe    | 468     | 78115      | 3712000000   |
| Angola      | Africa    | 1246700 | 20609294   | 100990000000 |
| ....        |           |         |            |              |





**Germany's population**

```SQL
SELECT population FROM world WHERE name = 'Germany';
```



**Per Capita GDP**

```SQL
SELECT name, gdp/population FROM world WHERE area > 5000000;
```



**Scandinavia**

```SQL
SELECT name, population FROM world WHERE name IN ('Ireland', 'Iceland', 'Danmark');
```



**Just the right size**

```SQL
SELECT name, area From world WHERE area BETWEEN 200000 AND 250000;
```





above context from [SELECT basics/zh - SQLZoo](https://www.sqlzoo.net/wiki/SELECT_basics/zh)

