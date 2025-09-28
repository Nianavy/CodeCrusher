https://www.nowcoder.com/exam/oj?page=1&tab=SQL%E7%AF%87&topicId=375
`SQL53`

```sql
SELECT 
    v.user_id,
    COUNT(v.info_id) AS visit_nums
FROM 
    visit_tb v
JOIN 
    (SELECT DISTINCT user_id 
     FROM order_tb 
     WHERE DATE(order_time) = '2022-09-02') o
ON 
    v.user_id = o.user_id
WHERE 
    DATE(v.visit_time) = '2022-09-02'
GROUP BY 
    v.user_id
ORDER BY 
    visit_nums DESC;
```
