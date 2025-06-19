https://www.hackerrank.com/challenges/weather-observation-station-8/

```SQL
SELECT DISTINCT CITY FROM STATION
 WHERE
   (CITY LIKE 'a%' or CITY LIKE 'e%' or CITY LIKE 'i%' or CITY LIKE 'o%' or CITY LIKE 'u%')
 AND
   (CITY LIKE '%a' or CITY LIKE '%e' or CITY LIKE '%i' or CITY LIKE '%o' or CITY LIKE '%u');
```

optimization version
```SQL
SELECT DISTINCT city
FROM station
WHERE city REGEXP '^[aeiou].*[aeiou]$';
```

