https://www.hackerrank.com/challenges/weather-observation-station-10

```SQL
-- normal version
SELECT DISTINCT city
FROM station
WHERE 
    city NOT LIKE '%a' AND
    city NOT LIKE '%e' AND
    city NOT LIKE '%i' AND
    city NOT LIKE '%o' AND
    city NOT LIKE '%u';


-- optimization version

SELECT DISTINCT city FROM station
WHERE city REGEXP '[^aeiou]$';
```
