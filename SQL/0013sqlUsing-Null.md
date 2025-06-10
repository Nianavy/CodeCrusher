# Using Null

| id編號 | dept學系 | name名字   | phone內線電話 | mobile流動電話 |
| :----- | :------- | :--------- | :------------ | :------------- |
| 101    | 1        | Shrivell   | 2753          | 07986 555 1234 |
| 102    | 1        | Throd      | 2754          | 07122 555 1920 |
| 103    | 1        | Splint     | 2293          |                |
| 104    |          | Spiregrain | 3287          |                |
| 105    | 2        | Cutflower  | 3212          | 07996 555 6574 |
| 106    |          | Deadyawn   | 3345          |                |
| ...    |          |            |               |                |

| id編號 | name名稱    |
| :----- | :---------- |
| 1      | Computing   |
| 2      | Design      |
| 3      | Engineering |
| ...    |             |





```SQL
SELECT name
FROM teacher
WHERE dept IS NULL;


SELECT teacher.name, dept.name
FROM teacher 
INNER JOIN dept ON (teacher.dept=dept.id);


SELECT teacher.name, dept.name
FROM teacher 
left JOIN dept ON teacher.dept = dept.id;


SELECT teacher.name, dept.name
FROM teacher
right JOIN dept ON teacher.dept = dept.id;


SELECT name, coalesce(mobile, '07986 444 2266') FROM teacher;


SELECT teacher.name, coalesce(dept.name, 'None')
FROM teacher
left JOIN dept ON teacher.dept = dept.id;


SELECT COUNT(name), COUNT(mobile) FROM teacher;


SELECT dept.name, count(teacher.name)
FROM teacher
right JOIN dept ON teacher.dept = dept.id
GROUP BY dept.name;


SELECT name,
CASE
WHEN dept = 1 OR dept = 2 THEN 'Sci'
ELSE 'Art' 
END
FROM teacher;


SELECT name, (
	CASE
	WHEN dept = 1 OR dept = 2 THEN 'Sci'
	WHEN dept = 3 THEN 'Art'
	ELSE 'None'
	END
)
FROM teacher;
```



above content from [Using Null/zh - SQLZoo](https://www.sqlzoo.net/wiki/Using_Null/zh)