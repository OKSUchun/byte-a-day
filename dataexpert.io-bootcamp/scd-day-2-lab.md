# SCD Day 2 Lab

####



```
// markdown
player_name | scoring_class | is_active | start_season | end_season | current_season
------------+---------------+-----------+--------------+------------+----------------
Alice       | A             | true      | 2018         | 2019       | 2021
Alice       | B             | true      | 2020         | 2020       | 2021
Alice       | B             | false     | 2021         | 2021       | 2021

```

```sql
postgresql

select * from players_scd;

```

* players\_scd 는 scoring\_class or is\_active 가 변경될때마다 1개의 row 가 추가됨
* 해당 성적이 유지되는 Start\_season과 end\_season 의 칼럼
* current\_season 은 집계하고 있는 지금의 연도임

<figure><img src="../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>



####

player\_scd 테이블은 2022 시즌까지 갱신 업데이트 하는 SCD Type 2 로직

기존 2021 이력과 2022 데이터를 합쳐서&#x20;

* 변하지 않은 속성은 end\_season 을 늘려주고
* 속성이 변한 경우(혹은 신규 플레이어)는 이전 이력을 닫고 새 버전(row)을 추가

이 두 번째 쿼리는 **`players_scd` 테이블을 2022 시즌까지 갱신(업데이트)하는 SCD(Slowly Changing Dimension, Type 2) 로직**이에요.

즉, **기존(2021) SCD 이력 + 2022년의 새 데이터**를 합쳐서

* 변하지 않은 속성은 기간(`end_season`)을 늘려 이어주고,
* 속성이 변한 경우(혹은 신규 플레이어)는 이전 이력을 닫고 새 버전(row)을 추가\
  하는 역할을 합니다.

***

#### 쿼리 단계별 설명

**1. `last_season_scd` (2021년 마지막 이력만 추출)**

```sql
sql복사편집SELECT * FROM players_scd
WHERE current_season = 2021
AND end_season = 2021
```

* `players_scd` 중 **2021년에 끝난 최신 버전(row)** 만 뽑음.
* 이게 2022 시즌과 비교할 기준선이 됨.

***

**2. `historical_scd` (2021년 이전에 이미 닫힌 이력)**

```sql
sql복사편집SELECT player_name, scoring_class, is_active, start_season, end_season
FROM players_scd
WHERE current_season = 2021
AND end_season < 2021
```

* 2021년보다 먼저 종료된 이력은 그냥 그대로 유지 (2022랑 상관없음).
* 최종 결과로 그대로 이어짐.

***

**3. `this_season_data` (2022년 새 데이터)**

```sql
sql복사편집SELECT * FROM players
WHERE current_season = 2022
```

* 2022 시즌의 원본 데이터 (플레이어별 `scoring_class`, `is_active`) 가져오기.

***

**4. `unchanged_records` (2021과 2022 속성이 같은 경우)**

```sql
sql복사편집SELECT ts.player_name,
       ts.scoring_class,
       ts.is_active,
       ls.start_season,
       ts.current_season as end_season
FROM this_season_data ts
JOIN last_season_scd ls ON ls.player_name = ts.player_name
WHERE ts.scoring_class = ls.scoring_class
  AND ts.is_active = ls.is_active
```

* 2021과 2022 **속성이 동일**한 경우:
  * 기존 `start_season`을 유지하고
  * `end_season`만 2022로 연장.

***

**5. `changed_records` (속성이 변했거나 신규 등장한 경우)**

```sql
sql복사편집SELECT ts.player_name,
       ts.scoring_class,
       ts.is_active,
       ls.is_active,
       ls.current_season as end_season,
       UNNEST(ARRAY[
           ROW(ls.scoring_class, ls.is_active, ls.start_season, ls.end_season)::scd_type,
           ROW(ts.scoring_class, ts.is_active, ts.current_season, ts.current_season)::scd_type
       ]) as records
FROM this_season_data ts
JOIN last_season_scd ls ON ls.player_name = ts.player_name
WHERE (ts.scoring_class <> ls.scoring_class)
   OR (ts.is_active <> ls.is_active)
   OR ls.player_name IS NULL
```

* **2021과 달라진 속성**이 있거나 **2022년 신규 플레이어**면:
  * `UNNEST`를 사용해 **두 개의 row를 생성**:
    1. 이전 버전 (2021 버전) → 이력으로 남김.
    2. 새로운 2022 버전 → 새 이력으로 시작.

***

**6. `unnested_changed_data`**

```sql
sql복사편집SELECT player_name,
       (records::scd_type).scoring_class,
       (records::scd_type).is_active,
       (records::scd_type).start_season,
       (records::scd_type).end_season
FROM changed_records
```

* 위에서 `UNNEST`로 펼친 **변경된/신규 row들**을 표준 컬럼 형태로 변환.

***

**7. `new_records` (완전 신규 플레이어 처리)**

```sql
sql복사편집SELECT ts.player_name,
       ts.scoring_class,
       ts.is_active,
       ts.current_season AS start_season,
       ts.current_season AS end_season
FROM this_season_data ts
LEFT JOIN last_season_scd ls
ON ls.player_name = ts.player_name
```

* 2022년에만 있고 2021에는 없던 **신규 플레이어**는\
  `start_season = end_season = 2022`로 시작.

***

**8. 최종 조합**

```sql
sql복사편집SELECT * FROM historical_scd
UNION ALL
SELECT * FROM unchanged_records
UNION ALL
SELECT * FROM unnested_changed_data
UNION ALL
SELECT * FROM new_records
ORDER BY player_name, start_season
```

* 네 가지 결과를 합쳐서 **2022년까지의 완성된 SCD 테이블**을 만듦.

***

#### **결과적으로 이 쿼리가 하는 일**

* 2021년까지의 `players_scd`를 기반으로\
  2022 시즌 데이터(`players`)를 반영.
* 변하지 않은 속성은 기간을 연장.
* 바뀐 속성이나 신규 플레이어는 **새 row를 추가**해서 SCD Type 2 방식으로 버전 관리.
* 최종적으로, **2022까지 업데이트된 이력 테이블**을 생성.
