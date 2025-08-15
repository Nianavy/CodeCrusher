

| yr   | subject    | winner                      |
| :--- | :--------- | :-------------------------- |
| 1960 | Chemistry  | Willard F. Libby            |
| 1960 | Literature | Saint-John Perse            |
| 1960 | Medicine   | Sir Frank Macfarlane Burnet |
| 1960 | Medicine   | Peter Madawar               |
| ...  |            |                             |



```SQL
SELECT yr, subject, winner FROM nobel WHERE yr = 1950;

SELECT winner FROM nobel WHERE yr = 1962 AND subject = 'Literature';

SELECT yr, subject FROM nobel WHERE winner = 'Albert Einstein';

SELECT winner FROM nobel WHERE yr >= 2000 AND subject = 'Peace';

SELECT yr, subject, winner FROM nobel WHERE yr <= 1989 AND yr >= 1980 AND subject = 'Literature';

SELECT yr, subject, winner FROM nobel WHERE winner in ('Theodore Roosevelt', 'Woodrow Wilson', 'Jimmy Carter');

SELECT winner FROM nobel WHERE winner LIKE 'John%';

SELECT yr, subject, winner FROM nobel WHERE (subject = 'physics' AND yr = 1980) OR (subject = 'chemistry' AND yr = 1984);

SELECT yr, subject, winner FROM nobel WHERE yr = 1980 AND subject != 'chemistry' AND subject != 'medicine';

SELECT yr, subject, winner FROM nobel WHERE (yr < 1910 AND subject = 'medicine') OR (yr >= 2004 AND subject = 'Literature');

SELECT yr, subject, winner FROM nobel WHERE winner = 'PETER GRÜNBERG';

SELECT * FROM nobel WHERE winner = 'EUGENE O''NEILL';

SELECT winner, yr, subject FROM nobel WHERE winner LIKE 'Sir%' ORDER BY yr DESC, winner ASC;

SELECT winner, subject FROM nobel WHERE yr = 1984 ORDER BY 
													CASE
														WHEN subject IN ('Chemistry', 'Physics') THEN 1
														ELSE 0
													END ASC,
													subject ASC,
													winner ASC;
```



above content from  [SELECT from Nobel Tutorial/zh - SQLZoo](https://www.sqlzoo.net/wiki/SELECT_from_Nobel_Tutorial/zh)