https://www.hackerrank.com/challenges/average-population-of-each-continent

```SQL
SELECT CO.Continent, FLOOR(AVG(C.Population)) FROM Country CO JOIN City C ON C.CountryCode = CO.Code GROUP BY CO.Continent;
```
