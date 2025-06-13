# NSS Tutorial



| Field               | Type         |
| :------------------ | :----------- |
| ukprn               | varchar(8)   |
| institution         | varchar(100) |
| subject             | varchar(60)  |
| level               | varchar(50)  |
| question            | varchar(10)  |
| A_STRONGLY_DISAGREE | int(11)      |
| A_DISAGREE          | int(11)      |
| A_NEUTRAL           | int(11)      |
| A_AGREE             | int(11)      |
| A_STRONGLY_AGREE    | int(11)      |
| A_NA                | int(11)      |
| CI_MIN              | int(11)      |
| score               | int(11)      |
| CI_MAX              | int(11)      |
| response            | int(11)      |
| sample              | int(11)      |
| aggregate           | char(1)      |



```SQL
SELECT A_STRONGLY_AGREE
  FROM nss
 WHERE question='Q01'
   AND institution='Edinburgh Napier University'
   AND subject='(8) Computer Science';
   
   
SELECT institution,subject
  FROM nss
 WHERE question='Q15'
   AND score>=100;
   
   
SELECT institution,score
  FROM nss
 WHERE question='Q15'
   AND score<50
   AND subject='(8) Computer Science'


SELECT subject,sum(response)
  FROM nss
 WHERE question='Q22'
   AND subject in('(8) Computer Science', '(H) Creative Arts and Design') group by subject;


SELECT subject,sum(response*A_STRONGLY_AGREE/100)
  FROM nss
 WHERE question='Q22'
   AND subject in('(8) Computer Science', '(H) Creative Arts and Design') group by subject;
   
   
SELECT subject,round(sum(response*A_STRONGLY_AGREE)/sum(response),0)
  FROM nss
 WHERE question='Q22'
   AND subject in('(8) Computer Science', '(H) Creative Arts and Design') 
   group by subject;
   

SELECT institution,round(sum(response*score )/sum(response),0)
  FROM nss
 WHERE question='Q22'
   AND (institution LIKE '%Manchester%')
group BY institution;


SELECT institution,sum(sample)
	,sum(case when subject =  '(8) Computer Science' then sample else 0 end)
  FROM nss
 WHERE question='Q01'
   AND (institution LIKE '%Manchester%')
group by institution;
```



above content from [NSS Tutorial - SQLZoo](https://www.sqlzoo.net/wiki/NSS_Tutorial)