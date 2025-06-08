# more-join-operation



```SQL
SELECT title, artist
  FROM album JOIN track
         ON (album.asin=track.album)
 WHERE song = 'Alison';
 
 
SELECT artist
FROM album JOIN track ON album.asin=track.album
WHERE song='Exodus';


SELECT song
FROM album JOIN track ON album.asin=track.album
WHERE title='Blur';


SELECT title,COUNT(song)
FROM album JOIN track ON (asin=album)
GROUP BY title;


SELECT title,COUNT(song)
FROM album JOIN track ON album.asin=track.album
WHERE song LIKE '%Heart%'
GROUP BY title;


SELECT song
FROM album JOIN track ON album.asin=track.album
WHERE song=title;


SELECT title FROM album
WHERE title=artist;


SELECT song, COUNT(DISTINCT album)
  FROM album JOIN track ON asin = album
GROUP BY song 
  HAVING COUNT( DISTINCT album) > 2;


SELECT title,price,COUNT(song)
FROM album JOIN track ON asin=album
GROUP BY title,price
HAVING price/COUNT(song) < 0.5;


SELECT album.title, COUNT(track.song) AS num
FROM album JOIN track
ON (album.asin = track.album)
GROUP BY album.title,track.album
ORDER BY num DESC;
```

above content from [Music Tutorial/zh - SQLZoo](https://www.sqlzoo.net/wiki/Music_Tutorial/zh)

