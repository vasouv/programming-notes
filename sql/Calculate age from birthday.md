 ## Postgresql
 With a table of people and birthdays, we want to calculate the age

| person  | birthday   |
| ------- | ---------- |
| vasilis | 1987-08-04 |
| pavlos  | 1999-08-03 |
| geo     | 2000-05-06 |
| eth     | 2015-03-15 |
```sql
select b."name", age(current_date, b.birthday)
from birthdays b
```
This query produces the period between the two dates. For example,

| name | period |
| --- | --- |
| vasilis | 38 years |
| pavlos | 26 years 1 day |
| geo | 25 years 2 mons 29 days |
| eth | 9 years 8 mons 22 days |
To extract the years, we can do
```sql
select b."name", extract(year from age(current_date, b.birthday))
from birthdays b
```
and so we get only the year part. Easy way to filter only the adults out.