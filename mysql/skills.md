# SQL技巧

##  1. 执行顺序

```sql
SELECT DISTINCT
    <select_list>
FROM
    <left_table> <join_type>
JOIN
    <right_table> ON <join_condition>
WHERE
    <where_condition>
GROUP BY
    <group_by_list>
HAVING
    <having_condition>
ORDER BY
    <order_by_condition>
LIMIT
    <limit_params>

# ------------------------------------

FROM <left_table>

ON  <join_condition>

<join_type> JOIN <right_table>

WHERE  <where_condition>

GROUP BY  <group_by_list>

HAVING  <having_condition>

SELECT DISTINCT  <select_list>

ORDER BY  <order_by_condition>

LIMIT <limit_param>
    
```
<br />

## 2. 正则表达式
- like 升级版
- 语法具体百度
```sql
    select * from tb where title like 'j%'
    # j开头
    select * from tb where title regexp '^j'
    # s结尾
    select * from tb where title regexp '$s'
    # 包含 a,b,c 中任意一个单词
    select * from tb where title regexp '[abc]'
```

<br />

## 3. MySQL常用函数
- 数字
- 字符串
- 日期
- 聚合

https://www.cnblogs.com/progor/p/8832663.html