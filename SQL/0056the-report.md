https://www.hackerrank.com/challenges/the-report

```SQL
SELECT CASE WHEN g.Grade < 8 THEN NULL ELSE s.Name END AS Name, g.Grade, s.Marks
FROM Students s JOIN Grades g ON s.Marks BETWEEN g.Min_Mark AND g.Max_Mark
ORDER BY g.grade DESC, CASE WHEN g.Grade >= 8 THEN s.Name ELSE s.Marks END ASC;
```
