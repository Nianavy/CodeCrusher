https://www.nowcoder.com/exam/oj?page=1&tab=SQL%E7%AF%87&topicId=375
`SQL41`

```sql
SELECT
    user_id,
    MAX(连续登录天数) AS max_consec_days
FROM
    (
        SELECT
            user_id,
            MAX(日期排序) - MIN(日期排序) + 1 AS 连续登录天数
        FROM
            (
                SELECT
                    user_id,
                    fdate,
                    ROW_NUMBER() OVER (
                        partition by
                            user_id
                        order by
                            fdate
                    ) AS 日期排序,
                    DATE_SUB(
                        fdate,
                        interval ROW_NUMBER() OVER (
                            partition by
                                user_id
                            order by
                                fdate
                        ) day
                    ) AS 初始日期
                FROM
                    tb_dau
                WHERE
                    fdate between '2023-01-01' AND '2023-01-31'
            ) AS t1
        GROUP by
            user_id,
            初始日期
    ) AS t2
GROUP BY
    user_id;
```
