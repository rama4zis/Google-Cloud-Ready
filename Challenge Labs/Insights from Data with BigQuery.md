# 1. Total confirmed cases

Open BigQuery in the Cloud Console.

Click Compose Query.
```sql
SELECT sum(cumulative_confirmed) as total_cases_worldwide FROM `bigquery-public-data.covid19_open_data.covid19_open_data` where date='2020-04-20' 
```
Make sure your date is correct like your challenge lab.

# 2. Worst affected areas

```sql
with deaths_by_states as (
SELECT subregion1_name as state, sum(cumulative_deceased) as death_count
FROM `bigquery-public-data.covid19_open_data.covid19_open_data` 
where country_name="United States of America" and date='2020-04-20' and subregion1_name is NOT NULL
group by subregion1_name
)

select count(*) as count_of_states
from deaths_by_states
where death_count > 300
```

How many states and the date maybe different in your challenge lab.

# 3. Identifying hotspots

```sql
SELECT subregion1_name as state, sum(cumulative_confirmed) as total_confirmed_cases 
FROM `bigquery-public-data.covid19_open_data.covid19_open_data` 
where country_name="United States of America" and date='2020-04-20' and subregion1_name is NOT NULL
group by subregion1_name
having total_confirmed_cases > 2500
order by total_confirmed_cases desc
```
Make sure the date and total confirmed cases are correct with your lab.

# 4. Fatality ratio

```sql
select sum(cumulative_confirmed) as total_confirmed_cases, sum(cumulative_deceased) as total_deaths, (sum(cumulative_deceased)/sum(cumulative_confirmed))*100 as case_fatality_ratio
from `bigquery-public-data.covid19_open_data.covid19_open_data`
where country_name="Italy" and date BETWEEN "2020-05-01" AND "2020-05-31"
```

Make sure the date is correct with your lab.

# 5. Identifying specific day
    
```sql
SELECT date
FROM `bigquery-public-data.covid19_open_data.covid19_open_data` 
where country_name="Italy" and cumulative_deceased>10000
order by date asc
limit 1
```

# 6. Finding days with zero net new cases

```sql
WITH india_cases_by_date AS (
  SELECT
    date,
    SUM( cumulative_confirmed ) AS cases
  FROM
    `bigquery-public-data.covid19_open_data.covid19_open_data`
  WHERE
    country_name ="India"
    AND date between '2020-02-25' and '2020-03-13'
  GROUP BY
    date
  ORDER BY
    date ASC 
 )
, india_previous_day_comparison AS 
(SELECT
  date,
  cases,
  LAG(cases) OVER(ORDER BY date) AS previous_day,
  cases - LAG(cases) OVER(ORDER BY date) AS net_new_cases
FROM india_cases_by_date
)
select count(*)
from india_previous_day_comparison
where net_new_cases=0
```

Make sure the date is correct with your lab.

# 7. Doubling rate

```sql
WITH us_cases_by_date AS (
  SELECT
    date,
    SUM(cumulative_confirmed) AS cases
  FROM
    `bigquery-public-data.covid19_open_data.covid19_open_data`
  WHERE
    country_name="United States of America"
    AND date between '2020-03-22' and '2020-04-20'
  GROUP BY
    date
  ORDER BY
    date ASC 
 )
, us_previous_day_comparison AS 
(SELECT
  date,
  cases,
  LAG(cases) OVER(ORDER BY date) AS previous_day,
  cases - LAG(cases) OVER(ORDER BY date) AS net_new_cases,
  (cases - LAG(cases) OVER(ORDER BY date))*100/LAG(cases) OVER(ORDER BY date) AS percentage_increase
FROM us_cases_by_date
)
select Date, cases as Confirmed_Cases_On_Day, previous_day as Confirmed_Cases_Previous_Day, percentage_increase as Percentage_Increase_In_Cases
from us_previous_day_comparison
where percentage_increase > 10
```

Make sure the percentage increase is correct with your lab.

# 8. Recovery rate

```sql  
WITH cases_by_country AS (
  SELECT
    country_name AS country,
    sum(cumulative_confirmed) AS cases,
    sum(cumulative_recovered) AS recovered_cases
  FROM
    bigquery-public-data.covid19_open_data.covid19_open_data
  WHERE
    date = '2020-05-10'
  GROUP BY
    country_name
 )
, recovered_rate AS 
(SELECT
  country, cases, recovered_cases,
  (recovered_cases * 100)/cases AS recovery_rate
FROM cases_by_country
)
SELECT country, cases AS confirmed_cases, recovered_cases, recovery_rate
FROM recovered_rate
WHERE cases > 50000
ORDER BY recovery_rate desc
LIMIT 10
```

# 9. CDGR - Cumulative daily growth rate

```sql
WITH
  france_cases AS (
  SELECT
    date,
    SUM(cumulative_confirmed) AS total_cases
  FROM
    `bigquery-public-data.covid19_open_data.covid19_open_data`
  WHERE
    country_name="France"
    AND date IN ('2020-01-24',
      '2020-04-20') -- This is the date of your challenge lab
  GROUP BY
    date
  ORDER BY
    date)
, summary as (
SELECT
  total_cases AS first_day_cases,
  LEAD(total_cases) OVER(ORDER BY date) AS last_day_cases,
  DATE_DIFF(LEAD(date) OVER(ORDER BY date),date, day) AS days_diff
FROM
  france_cases
LIMIT 1
)
select
    first_day_cases,
    last_day_cases,
    days_diff,
    POWER((last_day_cases/first_day_cases),(1/days_diff))-1 as cdgr
from summary
```

# 10. Create a Looker Studio report

```sql
SELECT
  date, SUM(cumulative_confirmed) AS country_cases,
  SUM(cumulative_deceased) AS country_deaths
FROM
  `bigquery-public-data.covid19_open_data.covid19_open_data`
WHERE
  date BETWEEN '2020-03-21'
  AND '2020-04-16'
  AND country_name ="United States of America"
GROUP BY date
```

Make sure the date is correct with your lab.