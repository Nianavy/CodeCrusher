# more SUM and COUNT **functions**

```SQL
SELECT COUNT(winner) FROM nobel;


SELECT DISTINCT(subject) FROM nobel;


SELECT COUNT(winner) FROM nobel WHERE subject = 'physics';


SELECT subject, COUNT(winner) FROM nobel GROUP BY subject;


SELECT subject, MIN(yr) AS first_award_year
FROM nobel
GROUP BY subject;


SELECT subject, COUNT(winner) FROM nobel WHERE yr = 2000 GROUP BY subject;


SELECT subject, COUNT(DISTINCT winner) AS unique_winners
FROM nobel
GROUP BY subject;


SELECT subject, COUNT(DISTINCT yr) FROM nobel GROUP BY subject;


SELECT yr
FROM nobel
WHERE subject = 'Physics'
GROUP BY yr
HAVING COUNT(winner) = 3;


SELECT winner
FROM nobel
GROUP BY winner
HAVING COUNT(*) > 1;


SELECT winner
FROM nobel
GROUP BY winner
HAVING COUNT(DISTINCT subject) > 1;


SELECT yr, subject
FROM nobel
WHERE yr >= 2000
GROUP BY yr, subject
HAVING COUNT(winner) = 3;
```

above content from [The nobel table can be used to practice more SUM and COUNT functions./zh - SQLZoo](https://www.sqlzoo.net/wiki/The_nobel_table_can_be_used_to_practice_more_SUM_and_COUNT_functions./zh)