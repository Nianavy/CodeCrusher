https://www.hackerrank.com/challenges/sql-projects

```SQL
SELECT 
    T1.Start_Date,
    T2.End_Date
FROM (
    SELECT ROW_NUMBER() OVER (ORDER BY Start_Date ASC) AS r,
    Start_Date
    FROM Projects
    WHERE (Start_Date NOT IN (SELECT End_Date FROM Projects))
) AS T1
LEFT JOIN 
    (SELECT ROW_NUMBER() OVER (ORDER BY End_Date ASC) AS r,
    End_Date
    FROM Projects
    WHERE (End_Date NOT IN (SELECT Start_Date FROM Projects))
) AS T2 ON T1.r=T2.r
ORDER BY DATEDIFF(T2.End_Date, T1.Start_Date) ASC, T1.Start_Date ASC;
```
