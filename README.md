```js
WITH routine_names AS (
    SELECT DISTINCT(Lower(proname)) AS name                              --#0
    FROM pg_proc
    WHERE proowner = To_Regrole(current_role)
)
SELECT
    name AS "Your routine...",
    Array_Remove(                                                        --#8
        Array(                                                           --#7
            SELECT Unnest(                                               --#5
                String_To_Array(                                         --#4
                    Regexp_Replace(                                      --#3
                        Regexp_Replace(                                  --#2
                            Lower(PG_Get_Functiondef(To_Regproc(name)))  --#1
                            , '--.*?\n', '', 'g'
                        )
                        , '\W+', ' ', 'g'
                    )
                    , ' '
                )
            )
            INTERSECT                                                    --#6
            SELECT name FROM routine_names
            ORDER BY 1
        )
        , name
    ) AS "...calls these routines:"
FROM
    routine_names;
```
