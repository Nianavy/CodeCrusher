# the JOIN operation

| id(編號) | mdate(日期)  | stadium(場館)             | team1(隊伍1) | team2(隊伍2) |
| :------- | :----------- | :------------------------ | :----------- | :----------- |
| 1001     | 8 June 2012  | National Stadium, Warsaw  | POL          | GRE          |
| 1002     | 8 June 2012  | Stadion Miejski (Wroclaw) | RUS          | CZE          |
| 1003     | 12 June 2012 | Stadion Miejski (Wroclaw) | GRE          | CZE          |
| 1004     | 12 June 2012 | National Stadium, Warsaw  | POL          | RUS          |
| ...      |              |                           |              |              |



| matchid(賽事編號) | teamid(隊伍編號) | player(入球球員)     | gtime(入球時間) |
| :---------------- | :--------------- | :------------------- | :-------------- |
| 1001              | POL              | Robert Lewandowski   | 17              |
| 1001              | GRE              | Dimitris Salpingidis | 51              |
| 1002              | RUS              | Alan Dzagoev         | 15              |
| 1001              | RUS              | Roman Pavlyuchenko   | 82              |
| ...               |                  |                      |                 |



| id(編號) | teamname(隊名) | coach(教練)      |
| :------- | :------------- | :--------------- |
| POL      | Poland         | Franciszek Smuda |
| RUS      | Russia         | Dick Advocaat    |
| CZE      | Czech Republic | Michal Bilek     |
| GRE      | Greece         | Fernando Santos  |
| ...      |                |                  |



```SQL
SELECT matchid, player FROM goal WHERE teamid = 'GER';


SELECT id,stadium,team1,team2 FROM game WHERE id = 1012;


SELECT player,teamid,stadium,mdate
  FROM game JOIN goal ON (id=matchid)
where teamid = 'GER';


SELECT team1, team2, player
  FROM game JOIN goal ON (id=matchid)
where player LIKE 'Mario%';


SELECT player, teamid, coach, gtime
  FROM goal JOIN eteam ON teamid = id
 WHERE gtime<=10;


SELECT mdate,teamname
FROM game JOIN eteam ON team1=eteam.id
WHERE coach='Fernando Santos';


SELECT player FROM game JOIN goal ON id=matchid
WHERE stadium='National Stadium, Warsaw';


SELECT DISTINCT player FROM goal JOIN(SELECT id FROM game WHERE team1 = 'GER' OR
team2 = 'GER')t ON (matchid=id) WHERE teamid != 'GER';


SELECT teamname, COUNT(matchid) FROM(SELECT teamname, matchid FROM eteam JOIN
goal ON (id=teamid))t GROUP BY teamname;


SELECT stadium, COUNT(player) FROM (SELECT stadium, player FROM game JOIN goal ON
(id=matchid))t GROUP BY stadium;


SELECT matchid, mdate, COUNT(teamid) FROM(SELECT matchid, mdate, team1, team2, teamid FROM
game JOIN goal ON matchid = id WHERE (team1 = 'POL' OR team2 = 'POL'))t GROUP BY matchid, mdate;


SELECT matchid,mdate,COUNT(teamid)
FROM(SELECT matchid,mdate, team1, team2,teamid
FROM game JOIN goal ON matchid = id
WHERE (team1 = 'GER' OR team2 = 'GER')) t
WHERE teamid='GER'
GROUP BY matchid,mdate;


SELECT mdate,team1,
SUM(CASE WHEN teamid=team1 THEN 1 ELSE 0 END) score1,
team2,
SUM(CASE WHEN teamid=team2 THEN 1 ELSE 0 END) score2
FROM game left JOIN goal ON matchid = id
group by mdate,matchid,team1,team2;
```







above content from [The JOIN operation/zh - SQLZoo](https://www.sqlzoo.net/wiki/The_JOIN_operation/zh)