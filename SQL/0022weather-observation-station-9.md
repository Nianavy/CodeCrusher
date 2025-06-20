https://www.hackerrank.com/challenges/weather-observation-station-9

```SQL
-- normal version
SELECT DISTINCT city
FROM station
WHERE 
    LOWER(city) NOT LIKE 'a%' AND
    LOWER(city) NOT LIKE 'e%' AND
    LOWER(city) NOT LIKE 'i%' AND
    LOWER(city) NOT LIKE 'o%' AND
    LOWER(city) NOT LIKE 'u%';

-- optimization version
SELECT DISTINCT city FROM station
WHERE city REGEXP '^[^aeiou]';
```
