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



#### dfdf

player\_scd 테이블은 2022 시즌까지 갱신 업데이트 하는 SCD Type 2 로직

기존 2021 이력과 2022 데이터를 합쳐서&#x20;

* 변하지 않은 속성은 end\_season 을 늘려주고
* 속성이 변한 경우(혹은 신규 플레이어)는 이전 이력을 닫고 새 버전(row)을 추가

```
// ㅊ
```

```
```
