https://www.hackerrank.com/challenges/asian-population

```SQL
SELECT SUM(x.population) AS Total_population FROM CITY AS x INNER JOIN COUNTRY AS y ON x.CountryCode = y.Code WHERE y.Continent = 'Asia';
```
