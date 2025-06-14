# Self join



站(編號,名稱) 路線(號碼,巴士公司名,方向,站)

| **stops** | **route** |
| :-------- | :-------- |
| id        | num       |
| name      | company   |
|           | pos       |
|           | stop      |
|           |           |



```SQL
SELECT COUNT(*) FROM stops;


SELECT id FROM stops WHERE name='Craiglockhart';


SELECT id, name
FROM stops INNER JOIN route ON stops.id = route.stop
WHERE company = 'LRT' AND num = '4';


SELECT company, num, COUNT(*)
FROM route WHERE stop=149 OR stop=53
GROUP BY company,num
HAVING count(*) = 2;


SELECT a.company, a.num, a.stop, b.stop
FROM route AS a INNER JOIN route AS b ON a.company = b.company 
WHERE a.stop = 53 AND b.stop = 149 AND a.num = b.num;


SELECT a.company, a.num, stopa.name, stopb.name
FROM route AS a JOIN route AS b ON a.company = b.company
INNER JOIN stops stopa ON a.stop = stopa.id INNER JOIN stops stopb ON b.stop = stopb.id
WHERE stopa.name = 'Craiglockhart' AND stopb.name = 'London Road' AND a.num = b.num;


SELECT DISTINCT a.company, a.num
FROM route AS a INNER JOIN route AS b ON a.num = b.num
AND a.company = b.company AND a.stop = 115 AND b.stop = 137;


SELECT DISTINCT a.company, a.num
FROM route AS a INNER JOIN route AS b ON (a.company=b.company AND a.num=b.num)
INNER JOIN stops stopa ON (a.stop=stopa.id)
INNER JOIN stops stopb ON (b.stop=stopb.id)
WHERE stopa.name='Craiglockhart' AND stopb.name = 'Tollcross';


SELECT DISTINCT stopb.name, a.company, a.num
FROM route AS a INNER JOIN route AS b ON (a.company=b.company AND a.num=b.num)
INNER JOIN stops stopa ON (a.stop=stopa.id)
INNER JOIN stops stopb ON (b.stop=stopb.id)
WHERE stopa.name='Craiglockhart' AND a.company = 'LRT';


select distinct start.num,start.company,name,end.num,end.company from (
   select a.num,a.company,b.stop from route as a
   join route as b on (a.company,a.num) = (b.company,b.num) and a.stop != b.stop
   where a.stop = (
       select id from stops where name = 'Craiglockhart'
   )
) as start
join (
   select c.num,c.company,c.stop from route as c
   join route as d on (c.company,c.num) = (d.company,d.num) and c.stop != d.stop
   where d.stop = (
       select id from stops where name = 'Sighthill'
   )
) as end on start.stop = end.stop
join stops on start.stop = stops.id;
```

above content from [Self join/zh - SQLZoo](https://www.sqlzoo.net/wiki/Self_join/zh)