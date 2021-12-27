# DVD_Rental_SQL

:pushpin: All_Questions
---

<details>
<summary>
Question 1. Percentage of revenue per movie medium
</summary>
  
## 📂 Instructions

:dvd: Write a query to return the percentage of revenue for each of the following films: film_id <= 10.

:dvd: Formula: revenue (film_id x) * 100.0/ revenue of all movies.

:dvd: The order of your results doesn't matter.

## 📂 Dataset

#### **```inventory```**
<details>
<summary>
View table
</summary>

Each row is unique; Inventoy_id is the primary key of the table
 
 |col_name | col_type|
 |-------- |--------|
 |inventory_id | integer|
 |film_id | smallint|
 |store_id | smallint|

</details>

#### **```payment```**

<details>
<summary>
View table
</summary>

Movie rental payment transactions table
 
 |col_name | col_type|
 |-------- |--------|
 payment_id | integer
 customer_id | smallint
 staff_id | smallint
 rental_id | integer
 amount | numeric
 payment_ts | timestamp with time zone

</details>

#### **```rental```**

<details>
<summary>
View table
</summary>

 |col_name | col_type|
 |-------- |--------|
 rental_id | integer
 rental_ts | timestamp with time zone
 inventory_id | integer
 customer_id | smallint
 return_ts | timestamp with time zone
 staff_id | smallint
  
 </details>

  
 ## 📂 Solution
 ----
  
 ```sql
 WITH movie_revenue AS (
SELECT 
 I.film_id, SUM(P.amount) revenue
FROM dvd_rentals.payment P
INNER JOIN dvd_rentals.rental R
ON R.rental_id = P.rental_id
INNER JOIN dvd_rentals.inventory I
ON I.inventory_id = R.inventory_id
GROUP BY I.film_id
)
SELECT film_id, round(revenue * 100.0 / SUM(revenue) OVER(),3) revenue_percentage
FROM movie_revenue
ORDER BY film_id
LIMIT 10
```
  
film_id | revenue_percentage
---|---
1 | 0.055
2 | 0.079
3 | 0.056
4 | 0.136
5 | 0.077
6 | 0.188
7 | 0.123
8 | 0.153
9 | 0.107
10 | 0.195
  
</details>
  
----

 <details>
<summary>
Question 2. Percentage of revenue per movie by category medium
</summary>
  
## 📂 Instructions

:dvd:Write a query to return the percentage of revenue for each of the following films: film_id <= 10 by its category.

:dvd:Formula: revenue (film_id x) * 100.0/ revenue of all movies in the same category.

:dvd:The order of your results doesn't matter.
                                                                                                      
:dvd:Return 3 columns: film_id, category name, and percentage.

## 📂 Dataset
                                                                                                      
#### **```inventory```**
<details>
<summary>
View table
</summary>
  
Each row is unique; Inventoy_id is the primary key of the table
 
 |col_name | col_type|
 |-------- |--------|
 |inventory_id | integer|
 |film_id | smallint|
 |store_id | smallint|

</details>

#### **```payment```**

<details>
<summary>
View table
</summary>

Movie rental payment transactions table
 
 |col_name | col_type|
 |-------- |--------|
 payment_id | integer
 customer_id | smallint
 staff_id | smallint
 rental_id | integer
 amount | numeric
 payment_ts | timestamp with time zone

</details>

#### **```rental```**

<details>
<summary>
View table
</summary>

 |col_name | col_type|
 |-------- |--------|
 rental_id | integer
 rental_ts | timestamp with time zone
 inventory_id | integer
 customer_id | smallint
 return_ts | timestamp with time zone
 staff_id | smallint
  
 </details>
 
#### **```film_category```**

<details>
<summary>
View table
</summary>
  
A film can only belong to one category
  
 col_name | col_type
 ----|----
 film_id | smallint
 category_id | smallint
  </details>
 
 #### **```film```**

<details>
<summary>
View table
</summary>
  
 col_name | col_type
 ----|----
 film_id | integer
 title | text
 description | text
 release_year | integer
 language_id | smallint
 original_language_id | smallint
 rental_duration | smallint
 rental_rate | numeric
 length | smallint
 replacement_cost | numeric
 rating | text
 
 </details>
 
 ## 📂 Solution
 ----
  
 ```sql
 WITH movie_revenue AS (
SELECT
 I.film_id, SUM(P.amount) revenue
FROM dvd_rentals.payment P
INNER JOIN dvd_rentals.rental R
ON R.rental_id = P.rental_id
INNER JOIN dvd_rentals.inventory I
ON I.inventory_id = R.inventory_id
GROUP BY I.film_id
)
SELECT 
 MR.film_id, 
 C.name category_name,
 round(revenue * 100.0 / SUM(revenue) OVER(PARTITION BY C.name),3) 
revenue_percent_category
FROM movie_revenue MR
INNER JOIN dvd_rentals.film_category FC
 ON FC.film_id = MR.film_id
INNER JOIN dvd_rentals.category C
 ON C.category_id = FC.category_id
ORDER BY film_id
LIMIT 10
;
```
  
film_id|category_name|revenue_percent_category
-----|-----|-----
1 | Documentary | 0.872
2|Horror|1.422
3|Documentary|0.898
4|Horror|2.465
5|Family|1.225
6|Foreign|2.969
7|Comedy|1.890
8|Horror|2.762
9|Horror|1.931
10|Sports|2.480
  
 </details>
 
 ---
 <details>
<summary>
Question 3. Movie rentals and average rentals in the same category
</summary>
  
## 📂 Instructions
  
:dvd:Write a query to return the number of rentals per movie, and the average number of rentals in its same category.

:dvd:You only need to return results for film_id <= 10.

:dvd:Return 4 columns: film_id, category name, number of rentals, and the average number of rentals from its category
                                                      
## 📂 Solution
 ----
```sql
WITH movie_rental AS (
 SELECT
 I.film_id,
 COUNT(*) rentals
 FROM dvd_rentals.rental R
 INNER JOIN dvd_rentals.inventory I
 ON I.inventory_id = R.inventory_id
 GROUP BY I.film_id
)
SELECT 
 film_id, 
 category_name, 
 rentals, 
 avg_rentals_category 
FROM (
SELECT
 MR.film_id,
 C.name category_name,
 rentals,
 round(AVG(rentals) OVER(PARTITION BY C.name),3) avg_rentals_category
FROM movie_rental MR
INNER JOIN dvd_rentals.film_category FC
 ON FC.film_id = MR.film_id
INNER JOIN dvd_rentals.category C
 ON C.category_id = FC.category_id
) X
```
                                                      
film_id|category_name|rentals|avg_rentals_category
 ---|---|---|---
7|Comedy|15|16.804
1|Documentary|23|16.667
3|Documentary|12|16.667
5|Family|12|16.358
6|Foreign|21|15.418
8|Horror|18|15.962
2|Horror|7|15.962
9|Horror|12|15.962
4|Horror|23|15.962
10|Sports|23|16.151        
                                                      
# Others
--Top store for movie sales
--Write a query to return the name of the store and its manager, that generated the most sales

select store, manager, total_sales
from dvd_rentals.sales_by_store
order by total_sales desc
limit 1;

--Top 3 movie categories by sales

select category, total_sales
from dvd_rentals.sales_by_film_category
order by total_sales desc
limit 3;

--Top 5 shortest movies

select title, length
from dvd_rentals.film
order by length asc
limit 5;

--Staff without a profile image

select first_name,last_name
from dvd_rentals.staff
where picture is null

--total movie rental revenue for each month

select 
    sum(amount) as total_revenue, 
    extract(year from payment_date) as year,
    extract(month from payment_date) as month_num
from dvd_rentals.payment
    group by year,month_num
    
--Daily revenue in June

select sum(amount) as daily_revenue, date(payment_date) as date_col
from dvd_rentals.payment
where extract(month from payment_date) = 6
group by date_col

--total number of unique customers for each month

select
  count(distinct customer_id) as total_customers, 
  extract(month from rental_date)as month
from dvd_rentals.rental
group by month

--Average customer spend by month

select 
  distinct customer_id, 
  extract(month from payment_date) as month,
  round(avg(amount),3) as avg_spend
from dvd_rentals.payment
group by customer_id, extract(month from payment_date)
order by customer_id

--count the number of customers who spend more than (>) $5 by month
select 
  extract(month from payment_date) as month_num,
  count(distinct customer_id) as total_customers,
  sum(amount) as total_amount
from dvd_rentals.payment
where amount > 5
group by extract(month from payment_date)

--Write a query to return the minimum and maximum customer total spend in June 2020
select min(amount), max(amount)
from dvd_rentals.payment
where extract(month from payment_date) = 6
  
--Find the number of actors whose last name is one of the following: 'DAVIS', 'BRODY', 'ALLEN', 'BERRY'
select first_name, last_name
from dvd_rentals.actor
where last_name in ('DAVIS', 'BRODY', 'ALLEN', 'BERRY')

--Actors' last name ending in 'EN' or 'RY' 
select last_name
from dvd_rentals.actor
where last_name like ('%EN') or last_name like('%RY')

--Write a query to return the number of actors whose first name starts with 'A', 'B', 'C', or others.
select first_name,
case 
  when first_name like('A%') then 'a_actor'
  when first_name like('B%') then 'b_actor'
  when first_name like('C%') then 'c_actor'
  else 'other'
end as actor_category
from dvd_rentals.actor
order by actor_category asc;

--Write a query to return the number of good days and bad days in May 2020 based on number of daily rentals.
with base as (select count(rental_id) as daily_count, date(rental_date) as daily_date
from dvd_rentals.rental
where extract(month from rental_date) = 7
group by daily_date)

select 
  sum(case
    when daily_count < 500 then 1
    else 0
    end) as bad_days,
  sum(case
    when daily_count > 500 then 1
    else 0
    end) as good_days
from base
      
--Fast movie watchers vs slow watchers
extract days and hours then add them then not null filter group by customer_id

with base as(
select 
  customer_id,
  extract(days from age(return_date, rental_date)) as days, 
  case when extract(hours from age(return_date, rental_date)) > 10 then 1 else 0 end as add_days
from dvd_rentals.rental
where return_date is not null),

avg_table as (
select ceiling(avg(days+add_days)) as total_days
from base 
group by customer_id)

select case when total_days > 5 then 'slow_watchers'
when total_days <= 5 then 'fast_watchers'
else null end as types_of_customers,
count(*) as total_watchers
from avg_table
group by types_of_customers

--Staff who live in Woodridge
select id, name
from dvd_rentals.staff_list where city = 'Woodridge'

--GROUCHO WILLIAMS’ actor_id
select actor_id
from dvd_rentals.actor_info
where last_name like '%WILLIAMS%' and first_name like'%GROUCHO'

--Write a query to return the film category id with the most films, as well as the number films in that category.
--top category and how many

select category_id, count(film_id) as total_films
from dvd_rentals.film_category
group by category_id
order by total_films desc
limit 1

--Write a query to return the first name and the last name of the actor who appeared in the most films
--films done by actors
select  first_name, last_name, count(film_id)as total_films
from dvd_rentals.film_actor f
inner join dvd_rentals.actor a
on f.actor_id = a.actor_id
group by f.actor_id, first_name, last_name
order by total_films desc
limit 1

--Write a query to return the first and last name of the customer who spent the most on movie rentals 
with base as (select customer_id, sum(amount) as total_spend
from dvd_rentals.payment 
group by customer_id
order by total_spend desc
limit 1)

select first_name, last_name
from dvd_rentals.customer C 
where c.customer_id in(select customer_id from base)

--Write a query to return the first and last name of the customer who made the most rental transactions in May
with base as (select customer_id, count(rental_id) as total_counts
from dvd_rentals.rental
where extract(month from rental_date) = 6 
group by customer_id
order by total_counts desc
limit 1)
select first_name, last_name from dvd_rentals.customer c
where c.customer_id in (select customer_id from base)

--Films with more than 10 actors
with base as (select film_id, count(actor_id) as total_actors
from dvd_rentals.film_actor
group by film_id
having count(actor_id)> 10)

select film_id, title
from dvd_rentals.film f 
where f. film_id in (select film_id from base)

--Write a query to return the title of the second shortest film based on its duration/length
with base as (select film_id, title, length
from dvd_rentals.film 
order by length asc
limit 2)

select title from base order by length desc limit 1

--Write a query to return the title of the film with the second largest cast
with base as (select film_id, count(actor_id) as casts
from dvd_rentals.film_actor
group by film_id
order by count(actor_id) desc 
limit 2)

select title 
from dvd_rentals.film f 
where f.film_id in (select film_id from base order by casts asc limit 1)

--Write a query to return the name of the customer who spent the second highest for movie rentals in May
with base as (
select customer_id, sum(amount) as total_spend
from dvd_rentals.payment
where extract(month from payment_date) = 5
group by customer_id
order by total_spend desc 
limit 2)

select first_name, last_name
from dvd_rentals.customer c
where c.customer_id in(select customer_id from base order by total_spend asc limit 1)



--number of customers who rented at least one movie in both May and June
with base as (select distinct customer_id as may_cust
from dvd_rentals.rental
where extract(month from rental_date) = 5)

select count(distinct customer_id) from dvd_rentals.rental 
where extract(month from rental_date) = 6 and customer_id in (select may_cust from base) 

--Write a query to return the titles of movies with more than >7 dvd copies in the inventory.
with base as (select film_id, count(inventory_id) as total_stock
from dvd_rentals.inventory
group by film_id
having count(inventory_id) > 7)

select title from dvd_rentals.film
where film_id in (select film_id from base)

--Write a query to return the number of films in the following categories: short, medium, and long based on length
select 
  case
    when length < 60 then 'short'
    when length > 100 then 'long'
    else 'medium'
  end as category_type,
  count(*) as film_numbers
from dvd_rentals.film
group by category_type

----Write a query to return the number of films with no rentals in July
--inventory            |film   |rental
--film_id, inventory_id|film_id|rental_id, inventory_id
with base as(
select distinct film_id
from dvd_rentals.inventory
where inventory_id in(select inventory_id from dvd_rentals.rental))

select count(film_id)as never_rented
from dvd_rentals.film
where film_id not in(select film_id from base )


                                                      
