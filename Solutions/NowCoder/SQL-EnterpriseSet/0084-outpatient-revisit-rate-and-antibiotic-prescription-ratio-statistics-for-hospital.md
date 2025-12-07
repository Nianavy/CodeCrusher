https://www.nowcoder.com/exam/oj?page=1&tab=SQL%E7%AF%87&topicId=375
`SQL125`

```sql
WITH revisit AS(SELECT 
v2.visit_id AS revisit_id
FROM visits AS v1
INNER JOIN visits AS v2
ON v1.patient_id = v2.patient_id
AND v1.dept = v2.dept
AND DATEDIFF(v2.visit_date, v1.visit_date) < 30
AND DATEDIFF(v2.visit_date, v1.visit_date) > 0)

SELECT
dept,
COUNT(DISTINCT v.visit_id) AS feb_2024_visits,
COUNT(DISTINCT pa.patient_id) AS feb_2024_unique_patients,
ROUND(COUNT(DISTINCT r.revisit_id) / COUNT(DISTINCT v.visit_id) * 100, 2) AS feb_2024_revisit_rate,
COALESCE(ROUND(SUM(is_antibiotic) / COUNT(is_antibiotic) * 100, 2), 0) AS feb_2024_antibiotic_rate
FROM patients AS pa
LEFT JOIN visits AS v
ON pa.patient_id = v.patient_id
LEFT JOIN prescriptions AS pr
ON pr.visit_id = v.visit_id
LEFT JOIN revisit AS r
ON v.visit_id = r.revisit_id
WHERE DATE_FORMAT(visit_date, '%Y-%m') = '2024-02'
GROUP BY dept;
```
