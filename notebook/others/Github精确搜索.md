# Github 精确搜索

## 项目组成

> + name : 项目名
>
> + description : 项目简要描述
>
> + README.md : 项目的详细情况介绍
>
> + 项目的源码

----

## 如何搜索

1. 搜索项目名中包含 React 的项目

   ```
   in:name React
   ```

2. 项目的 star 数大于5000

   ```
   in:name React stars:>5000
   ```

3. fork 数大于3000

   ```
   in:name React stars:>5000 forks:>3000
   ```

4. 搜索 README.md 里面包含 React 的项目

   ```
   in:readme React
   ```

5. 限制一下 star 和 fork 数

   ```
   in:readme React stars:>1000 forks:>200
   ```

6. 按照 description 进行搜索

   ```
   in:description 微服务
   ```

7. 限制语言为 python

   ```
   in:description 微服务 language:python
   ```

8. 限制最近更新时间为2020-01-01

   ```
   in:description 微服务 language:python pushed:>2020-01-01
   ```

## 总结

### 按内容搜索

> + in:name # 按照项目名搜索
> + in:readme # 按照README 搜索
> + in:description # 按照 description 搜索

### 筛选条件

>+ stars:>xxx # stars数大于xxx
>+ forks:>xxx # forks数大于xxx
>+ language:xxx # 编程语言是xxx
>+ pushed:>YYYY-MM-DD # 最后更新时间大于 YYYY-MM-DD

