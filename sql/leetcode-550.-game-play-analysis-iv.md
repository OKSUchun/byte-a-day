# Leetcode 550. Game Play Analysis IV



```sql
WITH total_player AS (select player_id
         , MIN(event_date) as first_login
      from Activity
    group by player_id -- O(N)
)
select round(sum(case when a.event_date is not null then 1
            else 0 end) /count(total_player.player_id),2) as fraction 
  from total_player
left join Activity as a
       on total_player.player_id = a.player_id
       AND total_player.first_login = DATE_SUB(a.event_date, INTERVAL 1 DAY)
       AND a.games_played >= 1
;

```



Though this solution is not efficient enough.&#x20;

<figure><img src="../.gitbook/assets/스크린샷 2025-05-17 오후 2.00.42.png" alt=""><figcaption></figcaption></figure>

#### Why is my Query so slow?&#x20;

* Grouping and aggregation is `O(N)`, where `N` is the number of rows in `Activity`.
* **Date arithmetic (`DATE_SUB`)** in the `JOIN` condition prevents using the index on `a.event_date`.
* **Inefficient filtering** occurs because, for each `total_player`, it scans `Activity` to match the player and shifted date.
* Without an index on `(player_id, event_date)` or `(player_id, games_played)`, this process is **O(P × A)**, where:
  * `P` is the number of unique players  -> Cannot be changed
  * `A` is the number of activity rows -> Can be changed



**Lesson Learned:**\
To enhance query efficiency, reduce the size of your tables.

&#x20;The `Activity` table has been optimized, resulting in fewer rows to match with `Total_Player`.&#x20;

```sql
# Write your MySQL query statement below
WITH total_player AS (select player_id
         , MIN(event_date) as first_login
      from Activity
    group by player_id
)
select round(sum(case when a.event_date is not null then 1
                       else 0 end) /count(total_player.player_id),2) as fraction 
  from total_player
left join (
    select *
      from Activity
      where games_played >= 1 --> change the number of activity rows
) as a
       on total_player.player_id = a.player_id
       AND total_player.first_login = DATE_SUB(a.event_date, INTERVAL 1 DAY)
;
```
