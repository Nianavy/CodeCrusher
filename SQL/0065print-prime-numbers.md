https://www.hackerrank.com/challenges/print-prime-numbers

```SQL
DELIMITER ^^

CREATE PROCEDURE prime_numbers()
BEGIN
        DECLARE n INT DEFAULT 3;
        DECLARE res VARCHAR(2000) DEFAULT "2";
        
        loop1: WHILE n <= 1000 DO
                SET @i = 2;
                SET @isPrime = 1;
                
                loop2: WHILE @i <= CEIL(n/2) DO
                        IF n % @i = 0 THEN
                            SET @isPrime  = 0;
                            LEAVE loop2;
                        END IF;
                        SET @i = @i+1;
                END WHILE loop2;
                
                IF @isPrime  = 1 THEN
                    SET res = CONCAT(res,"&",n);
                END IF;
                
                SET n = n+1;
        END WHILE loop1;
        
        SELECT res;
END ^^

DELIMITER ;

CALL prime_numbers();
```
