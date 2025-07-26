https://www.hackerrank.com/challenges/harry-potter-and-wands

```SQL
SELECT w.id, wp.age, w.coins_needed, w.power
FROM Wands w JOIN Wands_Property wp
    USING (code)
    JOIN (SELECT code, power, MIN(coins_needed) AS min_coins
         FROM Wands
         GROUP BY code, power) AS wtemp
     USING (code)          
WHERE wp.is_evil <> 1 AND w.coins_needed = wtemp.min_coins
ORDER BY w.power DESC, wp.age DESC;
```
