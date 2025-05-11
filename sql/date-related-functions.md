---
description: All the functions related to date
---

# DATE related functions

## 1. Add, Subtract date

```sql
// Some code
SELECT DATE_ADD('2020-01-01', INTERVAL 1 YEAR); -- ADD 1 YEAR
SELECT DATE_ADD('2020-01-01', INTERVAL 1 MONTH); -- ADD 1 MONTH
SELECT DATE_ADD('2020-01-01', INTERVAL 1 DAY); -- ADD 1 DAY

SELECT DATE_SUB('2020-01-01', INTERVAL 1 YEAR); -- ADD 1 YEAR
SELECT DATE_SUB('2020-01-01', INTERVAL 1 MONTH); -- ADD 1 MONTH
SELECT DATE_SUB('2020-01-01', INTERVAL 1 DAY); -- ADD 1 DAY
```



## 2. Calcaluate difference between two dates

The `TIMESTAMPDIFF` function in MySQL calculates the difference between two dates based on the unit specified.

<pre class="language-sql"><code class="lang-sql">// Some code
TIMESTAMPDIFF(unit, start_date, end_date)

<strong>SELECT TIMESTAMPDIFF(MONTH, '2020-11-01', '2020-12-01' ); -- RETURNS: 1
</strong></code></pre>

* **unit**: Specifies the unit for the difference calculation (e.g., SECOND, MINUTE, HOUR, DAY, WEEK, MONTH, QUARTER, YEAR).
* **datetime\_expr1 and datetime\_expr2**: Date expressions for calculation. The result is the difference `datetime_expr2 - datetime_expr1`.



## 3.  Custom Format DATETIME

```sql
SELECT DATE_FORMAT(given_date, format);
SELECT DATE_FORMAT('2020-05-05', '%Y-%m-01'); -- RETURNS '2020-05-01'
```

The DATE\_FORMAT function in MySQL formats given date in the pattern you want.

In this example, will format the given date (`'2020-05-05'`) in the pattern `'%Y-%m-01'`, resulting in `2020-05-01`, which sets the day to the first of the month.

