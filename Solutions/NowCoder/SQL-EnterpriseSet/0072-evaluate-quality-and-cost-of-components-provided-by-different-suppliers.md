https://www.nowcoder.com/exam/oj?page=1&tab=SQL%E7%AF%87&topicId=375
`SQL113`

```sql
SELECT a.supplier_id,b.supplier_name,c.component_name,a.quality_score,a.cost
FROM supply_quality_cost AS a
JOIN suppliers AS b ON a.supplier_id = b.supplier_id
JOIN components AS c ON a.component_id = c.component_ID
WHERE a.quality_score > 80 AND a.cost < 50
ORDER BY a.supplier_id ASC,a.cost ASC;
```
