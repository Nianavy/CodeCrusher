https://www.hackerrank.com/challenges/more-than-75-marks

```SQL
SELECT name FROM students 
WHERE marks > 75
ORDER BY RIGHT(name, 3), ID ASC;
```
