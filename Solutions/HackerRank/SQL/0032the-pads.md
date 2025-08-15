https://www.hackerrank.com/challenges/the-pads

```SQL
SELECT CONCAT(Name, "(",LEFT(Occupation, 1),")") FROM OCCUPATIONS
Order by Name;
SELECT CONCAT("There are a total of ", COUNT(*)," ", LOWER(OCCUPATION),'s.') FROM OCCUPATIONS
GROUP BY OCCUPATION
ORDER BY COUNT(*), OCCUPATION;
```
