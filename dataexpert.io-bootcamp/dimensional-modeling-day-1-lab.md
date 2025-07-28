# Dimensional Modeling Day 1 Lab

Making Cumulative Datapipeline

```sql

WITH last_season AS (
    SELECT * FROM players
    WHERE current_season = 1999

), this_season AS (
     SELECT * FROM player_seasons
    WHERE season = 2000
)
INSERT INTO players
SELECT
        COALESCE(ls.player_name, ts.player_name) as player_name,
        COALESCE(ls.height, ts.height) as height,
        COALESCE(ls.college, ts.college) as college,
        COALESCE(ls.country, ts.country) as country,
        COALESCE(ls.draft_year, ts.draft_year) as draft_year,
        COALESCE(ls.draft_round, ts.draft_round) as draft_round,
        COALESCE(ls.draft_number, ts.draft_number)
            as draft_number,
        COALESCE(ls.seasons,
            ARRAY[]::season_stats[]
            ) || CASE WHEN ts.season IS NOT NULL THEN
                ARRAY[ROW(
                ts.season,
                ts.pts,
                ts.ast,
                ts.reb, ts.weight)::season_stats]
                ELSE ARRAY[]::season_stats[] END
            as seasons,
         CASE
             WHEN ts.season IS NOT NULL THEN
                 (CASE WHEN ts.pts > 20 THEN 'star'
                    WHEN ts.pts > 15 THEN 'good'
                    WHEN ts.pts > 10 THEN 'average'
                    ELSE 'bad' END)::scoring_class
             ELSE ls.scoring_class
         END as scoring_class,
         CASE WHEN ts.season IS NOT NULL THEN 0
              ELSE ls.years_since_last_active + 1
                  END AS years_since_last_active,
         ts.season IS NOT NULL as is_active,
        2000

    FROM last_season ls
    FULL OUTER JOIN this_season ts
    ON ls.player_name = ts.player_name;

```

I wanted to backfill all the data from 1996 to 2022. need to write procedure to repeat 27 times of querying.&#x20;

```sql
select min(current_season), max(current_season)
  from players;

TRUNCATE players;
CREATE OR REPLACE PROCEDURE backfill_players()
    LANGUAGE plpgsql
AS $$
DECLARE
    y INTEGER;
BEGIN
    -- Loop from 1996 to 2022 (building each year sequentially)
    FOR y IN 1995..2022 LOOP
            -- Skip the first year because there's no "last season"
            IF y = 1995 THEN
                CONTINUE;
            END IF;

            -- Insert cumulative data for this year (y)
            EXECUTE format($sql$
            WITH last_season AS (
                SELECT * FROM players
                WHERE current_season = %1$s
            ),
            this_season AS (
                SELECT * FROM player_seasons
                WHERE season = %2$s
            )
            INSERT INTO players (
                player_name, height, college, country, draft_year,
                draft_round, draft_number, seasons, scoring_class,
                years_since_last_active, is_active, current_season
            )
            SELECT
                COALESCE(ls.player_name, ts.player_name),
                COALESCE(ls.height, ts.height),
                COALESCE(ls.college, ts.college),
                COALESCE(ls.country, ts.country),
                COALESCE(ls.draft_year, ts.draft_year),
                COALESCE(ls.draft_round, ts.draft_round),
                COALESCE(ls.draft_number, ts.draft_number),

                COALESCE(ls.seasons, ARRAY[]::season_stats[])
                || CASE WHEN ts.season IS NOT NULL THEN
                    ARRAY[ROW(
                        ts.season,
                        ts.pts,
                        ts.ast,
                        ts.reb,
                        ts.weight
                    )::season_stats]
                   ELSE ARRAY[]::season_stats[] END,

                CASE
                    WHEN ts.season IS NOT NULL THEN
                        (CASE WHEN ts.pts > 20 THEN 'star'
                              WHEN ts.pts > 15 THEN 'good'
                              WHEN ts.pts > 10 THEN 'average'
                              ELSE 'bad' END)::scoring_class
                    ELSE ls.scoring_class
                END,

                CASE WHEN ts.season IS NOT NULL
                     THEN 0
                     ELSE COALESCE(ls.years_since_last_active,0) + 1
                END,

                (ts.season IS NOT NULL)::boolean,

                %2$s  -- always the current season
            FROM last_season ls
            FULL OUTER JOIN this_season ts
            ON ls.player_name = ts.player_name
            ON CONFLICT (player_name, current_season) DO NOTHING;
        $sql$, y-1, y);  -- substitute last year (y-1) and this year (y)
        END LOOP;
END;
$$;
CALL backfill_players();
```

