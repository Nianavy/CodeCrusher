https://www.nowcoder.com/exam/oj?page=1&tab=SQL%E7%AF%87&topicId=375
`SQL114`

```sql
SELECT 
    a.product_id,
    SUM((a.unit_price - b.purchase_price) * a.quantity) AS total_profit,
    ROUND(AVG((a.unit_price - b.purchase_price)/b.purchase_price)*100, 2) AS profit_margin
FROM 
    sales_orders AS a
JOIN 
    purchase_prices AS b ON a.product_id = b.product_id
GROUP BY 
    a.product_id
ORDER BY 
    a.product_id ASC;
```
