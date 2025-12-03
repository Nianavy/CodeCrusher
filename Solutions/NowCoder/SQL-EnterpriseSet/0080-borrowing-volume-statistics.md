https://www.nowcoder.com/exam/oj?page=1&tab=SQL%E7%AF%87&topicId=375
`SQL121`

```sql
select
    book_id,
    book_title,
    sum(if(borrow_date between '2023-02-01' and '2023-02-28' ,1,0)) as feb_2023_borrows,
    sum(if(borrow_date between '2024-02-01' and '2024-02-29' ,1,0)) as feb_2024_borrows,
    sum(if(borrow_date between '2024-01-01' and '2024-01-31' ,1,0)) as jan_2024_borrows,
    sum(if(borrow_date between '2024-02-01' and '2024-02-29' ,1,0)) - sum(if(borrow_date between '2023-02-01' and '2023-02-28' ,1,0)) as yoy_delta,
    sum(if(borrow_date between '2024-02-01' and '2024-02-29' ,1,0)) - sum(if(borrow_date between '2024-01-01' and '2024-01-31' ,1,0)) as mom_delta,
    round(ifnull(sum(if(region='华北' and year(borrow_date)=2023, 1, 0)) / sum(if(year(borrow_date)=2023, 1, 0)) * 100,0),2) as north_pct_2023,
    round(ifnull(sum(if(region='华南' and year(borrow_date)=2023, 1, 0)) / sum(if(year(borrow_date)=2023, 1, 0)) * 100,0),2) as south_pct_2023,
    round(ifnull(sum(if(region='华东' and year(borrow_date)=2023, 1, 0)) / sum(if(year(borrow_date)=2023, 1, 0)) * 100,0),2) as east_pct_2023
from
    Books as b
    left join BorrowRecords as brs using (book_id)
    left join Branches as br using(branch_id)
group by book_id, book_title;
```
