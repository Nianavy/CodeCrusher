https://www.hackerrank.com/challenges/occupations

```SQL
SELECT
        MAX(CASE WHEN occupation = 'Doctor' THEN name END) AS doctor_names,
        MAX(CASE WHEN occupation = 'Professor' THEN name END) AS professor_names,
        MAX(CASE WHEN occupation = 'Singer' THEN name END) AS singer_names,
        MAX(CASE WHEN occupation = 'Actor' THEN name END) AS actor_names
FROM (
        SELECT
                name,
                occupation,
                ROW_NUMBER() OVER (PARTITION BY occupation ORDER BY name) as rn
        FROM OCCUPATIONS
) AS sorted
GROUP BY rn;
```
