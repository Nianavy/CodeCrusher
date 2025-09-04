https://www.nowcoder.com/exam/oj?page=1&tab=SQL%E7%AF%87&topicId=199
`SQL32`

```SQL
SELECT
    COUNT(date2) / COUNT(date1) AS avg_ret
FROM (
    SELECT
        DISTINCT qpd.device_id,
        qpd.date AS date1,
        uniq_id_date.date AS date2
    FROM
        question_practice_detail AS qpd
        LEFT JOIN (
            SELECT DISTINCT device_id, date
            FROM question_practice_detail
        ) AS uniq_id_date
    ON qpd.device_id = uniq_id_date.device_id
    AND date_add(qpd.date, interval 1 day) = uniq_id_date.date 
) AS id_last_next_date;
```
