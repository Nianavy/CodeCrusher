https://www.hackerrank.com/challenges/weather-observation-station-7/

```SQL
SELECT DISTINCT city FROM station WHERE
city LIKE '%a' OR city LIKE '%e' OR
city LIKE '%i' OR city LIKE '%o' OR city LIKE '%u';
```
optimization version
```SQL
SELECT DISTINCT city
FROM station
WHERE city REGEXP '[aeiou]$';
```
