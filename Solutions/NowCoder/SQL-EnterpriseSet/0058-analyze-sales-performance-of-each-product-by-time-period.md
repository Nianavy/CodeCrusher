https://www.nowcoder.com/exam/oj?page=1&tab=SQL%E7%AF%87&topicId=375
`SQL99`

```sql
 WITH temp AS(   
    SELECT DISTINCT
        a.product_id,
        a.product_name,
        a.category,
        round(coalesce(sum(b.total_amount) OVER (PARTITION BY a.product_id),0),2) AS q2_2024_sales_total,
        c.supplier_name
    FROM
        product_info AS a
        LEFT JOIN order_info AS b ON a.product_id = b.product_id
        AND b.order_date BETWEEN '2024-04-01' AND '2024-06-30'
        INNER JOIN supplier_info AS c ON a.product_id = c.product_id
 )

SELECT 
    product_id,
    product_name,
    q2_2024_sales_total,
    ROW_NUMBER() OVER (PARTITION BY category ORDER BY q2_2024_sales_total DESC) AS category_rank,
    supplier_name
FROM temp
ORDER BY product_id ASC;
```
