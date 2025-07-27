https://www.hackerrank.com/challenges/challenges

```SQL
WITH HackerChallengeCount AS (
    SELECT
        hacker_id,
        COUNT(challenge_id) AS challenge_count,
        COUNT(*) OVER (PARTITION BY COUNT(challenge_id)) AS same_challenge_count_count
    FROM
        Challenges
    GROUP BY
        hacker_id
)
SELECT
    hcc.hacker_id,
    h.name,
    hcc.challenge_count
FROM
    HackerChallengeCount hcc
    JOIN Hackers h ON hcc.hacker_id = h.hacker_id
WHERE
    NOT (
        hcc.same_challenge_count_count > 1 AND
        hcc.challenge_count < (SELECT MAX(challenge_count) FROM HackerChallengeCount)
    )
ORDER BY
    hcc.challenge_count DESC,
    hcc.hacker_id ASC;
```
