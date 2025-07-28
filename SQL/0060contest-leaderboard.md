https://www.hackerrank.com/challenges/contest-leaderboard

```SQL
SELECT 
    A.hacker_id, 
    A.name, 
    SUM(B.score) AS total_score
FROM 
    Hackers A
JOIN 
    (
        SELECT 
            hacker_id, 
            challenge_id, 
            MAX(score) AS score
        FROM 
            Submissions
        GROUP BY 
            challenge_id, 
            hacker_id
    ) B 
    ON A.hacker_id = B.hacker_id
GROUP BY 
    A.hacker_id, 
    A.name
HAVING 
    total_score > 0
ORDER BY 
    total_score DESC, 
    A.hacker_id ASC;
```
