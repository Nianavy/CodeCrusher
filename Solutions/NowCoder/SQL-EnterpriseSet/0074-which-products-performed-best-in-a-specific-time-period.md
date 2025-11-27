https://www.nowcoder.com/exam/oj?page=1&tab=SQL%E7%AF%87&topicId=375
`SQL115`

```sql
WITH t as(    
    SELECT a.product_id,b.product_name,
    sum(a.sales_amount) as total_sales_amount,
    sum(a.sales_quantity) as total_sales_quantity,
    DENSE_RANK() OVER (ORDER BY sum(a.sales_quantity) DESC) as rk
    FROM sales_records AS a
    JOIN products AS b ON a.product_id = b.product_id
    WHERE YEAR(a.sales_date) = 2024
    GROUP BY a.product_id
)

SELECT product_id,product_name,total_sales_amount,total_sales_quantity
FROM t 
WHERE rk = 1
ORDER BY product_id ASC;
```
