https://www.nowcoder.com/exam/oj?page=1&tab=SQL%E7%AF%87&topicId=199
`SQL41`

```SQL
SELECT
    profit_id,
    profit_date,
    profit,
SUM(profit) OVER (ORDER BY profit_date) AS cumulative_profit
FROM
    daily_profits;
```
