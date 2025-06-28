https://www.hackerrank.com/challenges/interviews

```SQL
SELECT
    C.contest_id,
    C.hacker_id,
    C.name,
    SUM(TotalSubmissions),
    SUM(TotalAcceptedSubmissions),
    SUM(TotalViews),
    SUM(TotalUniqueViews)
FROM
    Contests AS C
JOIN
    Colleges AS COL ON C.contest_id = COL.contest_id
JOIN
    Challenges AS CHA ON COL.college_id = CHA.college_id
LEFT JOIN
    (
        SELECT
            challenge_id,
            SUM(total_views) AS TotalViews,
            SUM(total_unique_views) AS TotalUniqueViews
        FROM View_Stats
        GROUP BY challenge_id
    ) AS VS ON CHA.challenge_id = VS.challenge_id
LEFT JOIN
    (
        SELECT
            challenge_id,
            SUM(total_submissions) AS TotalSubmissions,
            SUM(total_accepted_submissions) AS TotalAcceptedSubmissions
        FROM Submission_Stats
        GROUP BY challenge_id
    ) AS SS ON CHA.challenge_id = SS.challenge_id
GROUP BY
    C.contest_id, C.hacker_id, C.name
HAVING
    SUM(IFNULL(TotalSubmissions, 0)) +
    SUM(IFNULL(TotalAcceptedSubmissions, 0)) +
    SUM(IFNULL(TotalViews, 0)) +
    SUM(IFNULL(TotalUniqueViews, 0)) > 0
ORDER BY
    C.contest_id;
```
