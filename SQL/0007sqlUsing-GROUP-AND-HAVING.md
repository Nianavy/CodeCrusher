# Using GROUP AND HAVING

```SQL
SELECT * FROM nobel 
where winner = 'International Committee of the Red Cross';


SELECT winner 
FROM nobel 
WHERE winner != 'Toshihide Maskawa' 
  AND subject = 'Physics' 
  AND yr IN (
    SELECT yr 
    FROM nobel 
    WHERE winner = 'Toshihide Maskawa'
  );
  
  
SELECT winner 
FROM nobel 
WHERE subject = 'Economics'
  AND yr = (
    SELECT yr 
    FROM nobel 
    WHERE subject = 'Economics' 
    ORDER BY yr ASC 
    LIMIT 1
);


SELECT DISTINCT yr 
FROM nobel 
WHERE subject = 'Physics' 
  AND yr NOT IN (
    SELECT yr 
    FROM nobel 
    WHERE subject = 'Chemistry'
);


SELECT yr, subject, winner
FROM nobel
WHERE yr IN (
    SELECT yr
    FROM nobel
    GROUP BY yr
    HAVING COUNT(*) > 12
);


SELECT n1.winner, n1.yr, n1.subject
FROM nobel AS n1
WHERE n1.winner IN (
    SELECT winner
    FROM nobel
    GROUP BY winner
    HAVING COUNT(*) > 1
)
ORDER BY n1.winner ASC, n1.yr ASC;
```

above content from [The nobel table can be used to practice more subquery./zh - SQLZoo](https://www.sqlzoo.net/wiki/The_nobel_table_can_be_used_to_practice_more_subquery./zh)