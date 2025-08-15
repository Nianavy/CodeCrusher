https://www.hackerrank.com/challenges/binary-search-tree-1

```SQL
SELECT t.N,
      CASE
          WHEN t.N = (SELECT N FROM BST WHERE P is NULL) THEN "Root"
          WHEN COUNT(t.N)>1 THEN "Inner"
          WHEN COUNT(t.N) = 1 THEN "Leaf"
          END
FROM (
    SELECT N FROM BST
    UNION ALL
    SELECT P FROM BST
) AS t
WHERE t.N IS NOT NULL
GROUP BY (t.N)
ORDER BY t.N;
```
