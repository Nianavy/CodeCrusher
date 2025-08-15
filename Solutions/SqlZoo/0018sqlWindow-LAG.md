# Window LAG



```SQL
SELECT name, DAY(whn) as March, confirmed, deaths, recovered
FROM covid
WHERE name = 'Spain'
AND MONTH(whn) = 3 AND YEAR(whn) = 2020
ORDER BY whn;


SELECT name, DAY(whn), confirmed,
       LAG(confirmed,1) OVER (PARTITION BY name ORDER BY whn)
FROM covid
WHERE name = 'Italy'
AND MONTH(whn) = 3 AND YEAR(whn) = 2020
ORDER BY whn;


SELECT name, DAY(whn), 
       confirmed-LAG(confirmed, 1) OVER (PARTITION BY name ORDER BY whn)
FROM covid
WHERE name = 'Italy'
AND MONTH(whn) = 3 AND YEAR(whn) = 2020
ORDER BY whn;


SELECT name, DATE_FORMAT(whn,'%Y-%m-%d'), 
       confirmed-LAG(confirmed,1) OVER (partition by name ORDER BY whn)
FROM covid
WHERE name = 'Italy'
AND WEEKDAY(whn) = 0 AND YEAR(whn) = 2020
ORDER BY whn;


SELECT tw.name, DATE_FORMAT(tw.whn,'%Y-%m-%d'), tw.confirmed-lw.confirmed
FROM covid tw LEFT JOIN covid lw ON 
     DATE_ADD(lw.whn, INTERVAL 1 WEEK) = tw.whn
     AND tw.name=lw.name
WHERE tw.name = 'Italy' AND WEEKday(tw.whn)=0
ORDER BY tw.whn;


SELECT name, confirmed,
       RANK() OVER (ORDER BY confirmed DESC) as rc,
       deaths,
       RANK() OVER (ORDER BY deaths DESC) as 'death rank'
FROM covid
WHERE whn = '2020-04-20'
ORDER BY confirmed DESC;


SELECT name, round(infection_rate,2),
       RANK() OVER (ORDER BY infection_rate) 
FROM (select world.name, world.population,
      100000*confirmed/population as infection_rate
      from covid join world on covid.name=world.name
      where whn = '2020-04-20' and population >= 10000000)as a
ORDER BY population DESC;


select name, DATE_FORMAT(whn,'%Y-%m-%d'), peakNewCases
from
   (select name, whn, peakNewCases,
   rank() over (PARTITION BY name ORDER BY peakNewCases desc) as rank
   from
       (select name, whn,
       confirmed-LAG(confirmed, 1) OVER (PARTITION BY name ORDER BY whn)
       as peakNewCases
       from covid) as a
    where a.peakNewCases>1000) as b
where b.rank=1
order by whn;
```



above content from [窗口 LAG - SQLZoo --- Window LAG - SQLZoo](https://www.sqlzoo.net/wiki/Window_LAG)