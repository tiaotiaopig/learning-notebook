# SQL 基础语法

> 1. sql执行顺序
>
>    1. from > on > join > where > group by(开始使用select中的别名，后面的语句中都可以使用) > avg,sum.... > having > select > distinct > order by > limit
>
>    2. 各种子句
>
>       `<SELECT clause> [<FROM clause>] [<WHERE clause>] [<GROUP BY clause>] [<HAVING clause>] [<ORDER BY clause>] [<LIMIT clause>] `
>
>       开始->FROM子句->WHERE子句->GROUP BY子句->HAVING子句->SELECT子句->ORDER BY子句->LIMIT子句->最终结果 