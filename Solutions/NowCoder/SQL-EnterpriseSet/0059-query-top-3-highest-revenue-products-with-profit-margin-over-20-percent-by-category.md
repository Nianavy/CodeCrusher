https://www.nowcoder.com/exam/oj?page=1&tab=SQL%E7%AF%87&topicId=375
`SQL100`

```sql
WITH temp AS(    
    SELECT a.product_id,b.product_name,b.category_id,a.sales_amount,
    round((a.sales_amount - a.cost_amount)/a.sales_amount,2) profit_rate,
    ROW_NUMBER() OVER (PARTITION BY b.category_id ORDER BY a.sales_amount DESC,a.product_id ASC) AS rk
    FROM sales_and_profit AS a
    INNER JOIN product_category AS b ON a.product_id = b.product_id
)

SELECT product_id,product_name,category_id,sales_amount,profit_rate
FROM temp 
WHERE profit_rate > 0.2 AND rk <= 3
ORDER BY category_id ASC,sales_amount DESC,product_id ASC;
```
