https://www.hackerrank.com/challenges/draw-the-triangle-1

```SQL
DELIMITER ##

CREATE PROCEDURE p()
BEGIN
    DECLARE i INT DEFAULT 20;
    WHILE i >= 1 DO
        SELECT REPEAT('* ', i);
        SET i = i - 1;
    END WHILE;
END##

DELIMITER ;

CALL p();
```
