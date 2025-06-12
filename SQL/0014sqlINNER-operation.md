# Scottish-Parliament



| Name名字                | Party政黨 | Constituency選區    |
| ----------------------- | --------- | ------------------- |
| Adam MSP, Brian         | SNP       | North East Scotland |
| Aitken MSP, Bill        | Con       | Glasgow             |
| Alexander MSP, Ms Wendy | Lab       | Paisley North       |
| *...記錄總數: 129*      |           |                     |

| Code代碼          | Name政黨名   | Leader領導人             |
| ----------------- | ------------ | ------------------------ |
| Con               | Conservative | McLetchie MSP, David     |
| Green             | Green        |                          |
| Lab               | Labour       | Dewar MSP, Rt Hon Donald |
| *... 記錄總數: 9* |              |                          |



```SQL
SELECT name FROM msp
WHERE party IS NULL;


SELECT name, leader FROM party;


SELECT name, leader FROM party
WHERE leader IS NOT NULL;


way1:
SELECT p.name FROM party p
INNER JOIN msp m ON (p.code=m.party)
GROUP BY p.name;
way2:
SELECT DISTINCT(p.name) FROM party p
INNER JOIN msp m ON (p.code=m.party);


SELECT m.name, p.name AS 'party' FROM msp m
LEFT JOIN party p ON (m.party=p.code)
ORDER BY m.name;


SELECT p.name, COUNT(m.party) FROM msp m
INNER JOIN party p ON (m.party=p.code)
GROUP BY p.name;


SELECT p.name, COUNT(m.party) FROM msp m
RIGHT JOIN party p ON (m.party=p.code)
GROUP BY p.name;
```



above content from [Scottish Parliament/zh - SQLZoo](https://www.sqlzoo.net/wiki/Scottish_Parliament/zh)