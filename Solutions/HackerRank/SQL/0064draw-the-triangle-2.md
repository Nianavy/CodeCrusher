https://www.hackerrank.com/challenges/draw-the-triangle-2

```SQL
DELIMITER //
CREATE PROCEDURE MAKE_TRIANGLE(N INT, I INT)
    BEGIN
    SET max_sp_recursion_depth=50;
        IF I<=N THEN
        SELECT REPEAT('* ',I);
        CALL MAKE_TRIANGLE(N,I+1);
        END IF;
    END //
    DELIMITER ;
    CALL MAKE_TRIANGLE(20,1);
```
