Today SQLZOO can't access, so HackerRank





```SQL
-- 
SELECT * FROM city WHERE population > 100000 AND COUNTRYCODE = 'USA';

SELECT name FROM city WHERE population > 120000 AND countrycode = 'USA';

SELECT * FROM city;

SELECT * FROM city WHERE id = 1661;

SELECT * FROM city WHERE countrycode = 'JPN';

SELECT name FROM city WHERE countrycode = 'JPN';

SELECT city, state FROM station;

SELECT DISTINCT city FROM station WHERE id % 2 = 0;

SELECT
    COUNT(city) - COUNT(DISTINCT city) AS difference
FROM station;

(
    SELECT 
        CITY, 
        LENGTH(CITY) AS LENGTH
    FROM STATION
    ORDER BY LENGTH(CITY) ASC, CITY ASC
    LIMIT 1
)
UNION
(
    SELECT 
        CITY, 
        LENGTH(CITY) AS LENGTH
    FROM STATION
    ORDER BY LENGTH(CITY) DESC, CITY ASC
    LIMIT 1
);
```



above content from [Solve SQL | HackerRank](https://www.hackerrank.com/domains/sql)