https://www.nowcoder.com/exam/oj?page=1&tab=SQL%E7%AF%87&topicId=375
`SQL109`

```sql
WITH
    temp AS (
        SELECT
            a.destination_city,
            b.transport_name,
            DATEDIFF(a.delivery_date, a.order_date) AS duration,
            c.total_cost
        FROM
            order_info AS a
            INNER JOIN transport_detail AS b ON a.transport_id = b.transport_id
            INNER JOIN cost_data AS c ON a.order_id = c.order_id
    )
SELECT
    destination_city,
    transport_name,
    round(avg(duration), 2) average_transport_duration,
    round(sum(total_cost), 2) total_transport_cost
FROM
    temp
GROUP BY
    destination_city,
    transport_name
ORDER BY
    destination_city ASC,
    transport_name ASC;
```
