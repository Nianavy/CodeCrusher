https://www.nowcoder.com/exam/oj?page=1&tab=SQL%E7%AF%87&topicId=375
`SQL123`

```sql
WITH t1 AS (
    SELECT 
        c.campaign_id,
        campaign_name,
        SUM(IF(DATE_FORMAT(event_time, '%Y-%m') = '2023-02', 1, 0)) conv_2023_02,
        SUM(IF(DATE_FORMAT(event_time, '%Y-%m') = '2024-02', 1, 0)) conv_2024_02,
        SUM(IF(DATE_FORMAT(event_time, '%Y-%m') = '2024-01', 1, 0)) conv_2024_01,
        IFNULL(ROUND(SUM(IF(Year(event_time) = 2023 AND region = '华北', 1, 0))*100/SUM(IF(Year(event_time) = 2023, 1, 0)), 2), 0.00) north_pct_2023,
        IFNULL(ROUND(SUM(IF(Year(event_time) = 2023 AND region = '华南', 1, 0))*100/SUM(IF(Year(event_time) = 2023, 1, 0)), 2), 0.00) south_pct_2023,
        IFNULL(ROUND(SUM(IF(Year(event_time) = 2023 AND region = '华东', 1, 0))*100/SUM(IF(Year(event_time) = 2023, 1, 0)), 2), 0.00) east_pct_2023
    FROM Events e 
    JOIN Campaigns c ON e.campaign_id = c.campaign_id
    JOIN Users u ON e.user_id = u.user_id
    WHERE event_type = 'signup'
    GROUP BY campaign_id
),
t2 AS (
    SELECT 
        a.campaign_id,
        ROUND(SUM(TIMESTAMPDIFF(MINUTE, click_time, signup_time))/COUNT(DISTINCT a.user_id), 2) avg_click_to_signup_min_2024_02
    FROM(
        SELECT 
            campaign_id,
            user_id,
            MAX(event_time) click_time
        FROM Events 
        WHERE  event_type = 'click'
        GROUP BY campaign_id, user_id
        ) a
    JOIN(
        SELECT 
            campaign_id,
            user_id,
            MAX(event_time) signup_time
        FROM Events  
        WHERE event_type = 'signup'
        GROUP BY campaign_id, user_id 
        ) b ON a.user_id = b.user_id AND a.campaign_id = b.campaign_id 
    WHERE click_time <= signup_time
    GROUP BY campaign_id
),
t3 AS (
    SELECT 
        campaign_id,
        channel,
        ROW_NUMBER()OVER(PARTITION BY campaign_id ORDER BY COUNT(event_type = 'signup') DESC, 
        CASE
            WHEN channel='app' THEN 1
            WHEN channel='mini' THEN 2
            WHEN channel='web' THEN 3
        END ) rk
    FROM Events 
    WHERE DATE_FORMAT(event_time, '%Y-%m') = '2024-02'
    GROUP BY campaign_id, channel
)
SELECT
    t1.campaign_id,
    t1.campaign_name,
    conv_2023_02,
    conv_2024_02,
    conv_2024_01,
    conv_2024_02 - conv_2023_02 yoy_delta,
    conv_2024_02 - conv_2024_01 mom_delta,
    north_pct_2023,
    south_pct_2023,
    east_pct_2023,
    avg_click_to_signup_min_2024_02,
    channel top_channel_2024_02
FROM t1 
JOIN t2 ON t1.campaign_id = t2.campaign_id 
JOIN t3 ON t1.campaign_id = t3.campaign_id 
WHERE rk = 1
ORDER BY t1.campaign_id;
```
