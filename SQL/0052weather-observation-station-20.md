https://www.hackerrank.com/challenges/weather-observation-station-20

```SQL
SELECT ROUND(lat_n, 4) FROM (
    SELECT lat_n, ROW_NUMBER() OVER(
        ORDER BY lat_n
    ) AS row_num
    FROM station
) AS sub_table
WHERE row_num = (SELECT CEILING(COUNT(*) / 2) FROM station);
```
