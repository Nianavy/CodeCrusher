# More-JOIN-operations-II

```SQL
SELECT  id,title FROM movie WHERE yr=1962;


select yr
from movie
where title='Citizen Kane';


select id,title,yr
from movie
where title like 'Star Trek%'
order by yr;


select title
from movie
where id in(11768,11955,21191);


select id
from actor
where name='Glenn Close';


SELECT id FROM movie
WHERE title='Casablanca';


SELECT name
FROM actor JOIN casting
ON id =actorid
WHERE movieid=11768;


select name from casting
join actor a  on (a.id = actorid)
join movie m on (m.id = movieid)  
where title = 'Alien';


select title from casting
join actor a  on (a.id = actorid)
join movie m on (m.id = movieid)  
where name='Harrison Ford';


select title from casting
join actor a  on (a.id = actorid)
join movie m on (m.id = movieid)  
where name='Harrison Ford' and ord!=1;


select title,name from casting
join actor a  on (a.id = actorid)
join movie m on (m.id = movieid)  
where yr=1962 and ord=1;


SELECT yr, COUNT(title)
FROM movie
JOIN casting ON movie.id = casting.movieid
JOIN actor ON casting.actorid = actor.id
WHERE name = 'John Travolta'
GROUP BY yr
HAVING COUNT(title) = (
    SELECT MAX(c)
    FROM (
        SELECT yr, COUNT(title) AS c
        FROM movie
        JOIN casting ON movie.id = casting.movieid
        JOIN actor ON casting.actorid = actor.id
        WHERE name = 'John Travolta'
        GROUP BY yr
    ) AS t
);


select title,name
from casting join movie m on (m.id=movieid)
join actor a on (a.id=actorid)
where ord=1 and movieid in(
select movieid from casting join actor a on (a.id=actorid) where name='Julie Andrews');


select distinct name
from casting join movie m on (m.id = movieid)
join actor a on (a.id = actorid)
where actorid in
(select actorid from casting where ord = 1
group by actorid
having count(actorid)
>=30)order by name;


select title,count(title) as c
from casting join movie m on (m.id = movieid)
where movieid in (select id from movie where yr = 1978)
group by title order by c desc;


select distinct name
from actor join casting on (id = actorid)
where name != 'Art Garfunkel' and movieid in
(select movieid from casting  join actor on(id = actorid) where name = 'Art Garfunkel');
```

above content from [More JOIN operations/zh - SQLZoo](https://www.sqlzoo.net/wiki/More_JOIN_operations/zh)