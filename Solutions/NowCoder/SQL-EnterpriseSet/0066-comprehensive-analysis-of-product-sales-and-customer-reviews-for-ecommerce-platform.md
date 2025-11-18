https://www.nowcoder.com/exam/oj?page=1&tab=SQL%E7%AF%87&topicId=375
`SQL107`

```sql
select
    product_id,
    product_name,
    sum(quantity) as total_quantity,
    round(average_rating, 2) as average_rating
from
    sales_underline
    left join products_underline using (product_id)
    join (
        select
            product_id,
            avg(rating) as average_rating
        from
            reviews_underline
        group by
            product_id
        having
            average_rating <= 4
    ) as t using (product_id)
group by
    product_id,
    product_name
order by
    average_rating ASC,
    product_id ASC;
```
