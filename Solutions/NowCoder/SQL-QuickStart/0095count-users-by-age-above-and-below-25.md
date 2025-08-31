https://www.nowcoder.com/exam/oj?page=1&tab=SQL%E7%AF%87&topicId=199
`SQL29`

```SQL
SELECT 
    CASE 
        WHEN age < 25 OR age IS NULL THEN '25岁以下'
        ELSE '25岁及以上'
    END AS age_cut,
    COUNT(*) AS number
FROM user_profile
GROUP BY 
    CASE 
        WHEN age < 25 OR age IS NULL THEN '25岁以下'
        ELSE '25岁及以上'
    END;
```
