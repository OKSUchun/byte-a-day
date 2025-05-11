---
icon: database
---

# How to Pivot with MySQL

## what is pivot?

**Pivoting** in data analysis refers to summarizing and restructuring data to gain insights. It involves aggregating data based on key columns, allowing you to calculate sums, counts, or averages, transforming the dataset into a more readable format.



For instance...

```sql
CREATE TABLE sales (
  order_month DATE,        -- 2024-01-01, 2024-02-01 …
  category    VARCHAR(50), -- 'electronics', 'fashion', 'grocery'
  amount      INT
);
```

If you want a pivot table like...

| order\_month | electronics                | fashion                | grocery                |
| ------------ | -------------------------- | ---------------------- | ---------------------- |
| 2020-01-01   | amount of electronics sold | amount of fashion sold | amount of grocery sold |
| 2020-02-01   |                            |                        |                        |
| 2020-03-01   |                            |                        |                        |

```sql
SELECT order_month
     , SUM(IF(category='electronics', amount, 0)) AS electronics
     , SUM(IF(category='fashion', amount, 0)) AS fashion
     , SUM(IF(category='grocery', amount, 0)) AS grocery 
  FROM sales
GROUP BY order_month;

```

When you need to aggregate data only on condition,&#x20;

1. ```sql
   GROUP BY Row_COLUMN -- in last case, order_month
   SUM(IF(Column_condition , Value, 0)) AS 열이름 -- column_condition : 'electronics', value: 'amount' 
   ```
