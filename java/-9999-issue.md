# issue

## mysql不用唯一约束的解决办法

```sql
INSERT INTO
    t_content_tag ( `name` )
SELECT
    "NBA12"
FROM
    DUAL
WHERE
    NOT EXISTS (
        SELECT
            `name`
        FROM
            t_content_tag
        WHERE
            `name` = "NBA12"
        )
```

> DUAL 虚拟表