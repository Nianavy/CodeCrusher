https://www.nowcoder.com/exam/oj?page=1&tab=SQL%E7%AF%87&topicId=375
`SQL111`

```sql
SELECT DISTINCT a.courier_id,b.courier_name,
b.base_salary + sum(a.delivery_fee) OVER (PARTITION BY courier_id) AS total_income
FROM deliveries_info AS a 
JOIN couriers_info AS b ON a.courier_id = b.courier_id
WHERE MONTH(a.delivery_date) = '7'
ORDER BY courier_id ASC;
```
