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

> DUAL虚拟表

## tomcat无法启动

- org.apache.catalina.loader.WebappClassLoaderBase loadClass
  - 依赖的jar的版本错误
  - 依赖问题
