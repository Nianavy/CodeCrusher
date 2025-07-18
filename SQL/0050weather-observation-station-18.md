https://www.hackerrank.com/challenges/weather-observation-station-18

```SQL
select abs(round((min(lat_n)-max(lat_n)) + (min(long_w)-max(long_w)),4)) from station;
```
