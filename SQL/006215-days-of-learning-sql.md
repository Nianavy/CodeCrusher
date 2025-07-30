https://www.hackerrank.com/challenges/15-days-of-learning-sql

```SQL
SELECT
    dates.submission_date,
    IFNULL(consistent_counts.hacker_count, 0) AS distinct_hacker_count,
    top_hacker.hacker_id,
    top_hacker.name
FROM
    (SELECT DISTINCT submission_date FROM submissions) AS dates
LEFT JOIN
    (
        SELECT
            s1.submission_date,
            COUNT(s1.hacker_id) AS hacker_count
        FROM
            (SELECT DISTINCT submission_date, hacker_id FROM submissions) AS s1
        WHERE
            (SELECT COUNT(DISTINCT s2.submission_date)
             FROM submissions s2
             WHERE s2.hacker_id = s1.hacker_id AND s2.submission_date <= s1.submission_date
            ) = DATEDIFF(s1.submission_date, '2016-03-01') + 1
        GROUP BY
            s1.submission_date
    ) AS consistent_counts ON dates.submission_date = consistent_counts.submission_date
LEFT JOIN
    (
        SELECT
            daily_counts.submission_date,
            MIN(daily_counts.hacker_id) AS hacker_id,
            (SELECT name FROM hackers WHERE hacker_id = MIN(daily_counts.hacker_id)) AS name
        FROM
            (
                SELECT submission_date, hacker_id, COUNT(*) AS subs_count
                FROM submissions
                GROUP BY submission_date, hacker_id
            ) AS daily_counts
        JOIN
            (
                SELECT submission_date, MAX(subs_count) AS max_subs
                FROM (
                    SELECT submission_date, hacker_id, COUNT(*) AS subs_count
                    FROM submissions
                    GROUP BY submission_date, hacker_id
                ) AS inner_counts
                GROUP BY submission_date
            ) AS max_counts ON daily_counts.submission_date = max_counts.submission_date
                            AND daily_counts.subs_count = max_counts.max_subs
        GROUP BY
            daily_counts.submission_date
    ) AS top_hacker ON dates.submission_date = top_hacker.submission_date
ORDER BY
    dates.submission_date;
```
