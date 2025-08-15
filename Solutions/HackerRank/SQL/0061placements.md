https://www.hackerrank.com/challenges/placements

```SQL
SELECT
    s.name
FROM
    students s,
    friends f,
    packages p1,
    packages p2
WHERE
    s.id = f.id
    AND p1.id = f.id
    AND p2.id = f.friend_id
    AND p1.salary < p2.salary
ORDER BY
    p2.salary ASC;
```
