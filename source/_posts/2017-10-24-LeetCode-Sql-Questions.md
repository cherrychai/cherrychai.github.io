---
layout: post
title: LeetCode - Some Boring Sql Questions
comments: true
author: "柴华"
date: 2017-10-24
header-img: 
categories : [LeetCode]
tags: [Sql]
---

> [595. Big Countries](https://leetcode.com/problems/big-countries/)
>
> There is a table World
>
>     +-----------------+------------+------------+--------------+---------------+
>     | name            | continent  | area       | population   | gdp           |
>     +-----------------+------------+------------+--------------+---------------+
>     | Afghanistan     | Asia       | 652230     | 25500100     | 20343000      |
>     | Albania         | Europe     | 28748      | 2831741      | 12960000      |
>     | Algeria         | Africa     | 2381741    | 37100000     | 188681000     |
>     | Andorra         | Europe     | 468        | 78115        | 3712000       |
>     | Angola          | Africa     | 1246700    | 20609294     | 100990000     |
>     +-----------------+------------+------------+--------------+---------------+
>
> A country is big if it has an area of bigger than 3 million square km or a population of more than 25 million.
>
> Write a SQL solution to output big countries' name, population and area.
>
> For example, according to the above table, we should output:
>
>     +--------------+-------------+--------------+
>     | name         | population  | area         |
>     +--------------+-------------+--------------+
>     | Afghanistan  | 25500100    | 652230       |
>     | Algeria      | 37100000    | 2381741      |
>     +--------------+-------------+--------------+
<!--more-->

###### Answer:
``` sql
SELECT name,population,area FROM World WHERE area > 3000000 OR population > 25000000;
```

> [627. Swap Salary](https://leetcode.com/problems/swap-salary/)
>
> Given a table salary, such as the one below, that has m=male and f=female values. Swap all f and m values (i.e., change all f values to m and vice versa) with a single update query and no intermediate temp table.
>
> For example:
>
>     | id | name | sex | salary |
>     |----|------|-----|--------|
>     | 1  | A    | m   | 2500   |
>     | 2  | B    | f   | 1500   |
>     | 3  | C    | m   | 5500   |
>     | 4  | D    | f   | 500    |
>
> After running your query, the above salary table should have the following rows:
>
>     | id | name | sex | salary |
>     |----|------|-----|--------|
>     | 1  | A    | f   | 2500   |
>     | 2  | B    | m   | 1500   |
>     | 3  | C    | f   | 5500   |
>     | 4  | D    | m   | 500    |

###### Answer:
``` sql
UPDATE salary SET sex = (CASE WHEN sex = 'm' THEN 'f' ELSE 'm' END);
```

> [620. Not Boring Movies](https://leetcode.com/problems/not-boring-movies/)
>
> X city opened a new cinema, many people would like to go to this cinema. The cinema also gives out a poster indicating the movies’ ratings and descriptions.
>
> Please write a SQL query to output movies with an odd numbered ID and a description that is not 'boring'. Order the result by rating.
>
> For example, table cinema:
>
>     +---------+-----------+--------------+-----------+
>     |   id    | movie     |  description |  rating   |
>     +---------+-----------+--------------+-----------+
>     |   1     | War       |   great 3D   |   8.9     |
>     |   2     | Science   |   fiction    |   8.5     |
>     |   3     | irish     |   boring     |   6.2     |
>     |   4     | Ice song  |   Fantacy    |   8.6     |
>     |   5     | House card|   Interesting|   9.1     |
>     +---------+-----------+--------------+-----------+
>
> For the example above, the output should be:
>
>     +---------+-----------+--------------+-----------+
>     |   id    | movie     |  description |  rating   |
>     +---------+-----------+--------------+-----------+
>     |   5     | House card|   Interesting|   9.1     |
>     |   1     | War       |   great 3D   |   8.9     |
>     +---------+-----------+--------------+-----------+

###### Answer:
``` sql
SELECT * FROM cinema WHERE id % 2 = 1 AND description != "boring" ORDER BY rating DESC;
SELECT * FROM cinema WHERE MOD(id,2)=1 AND description != "boring" ORDER BY rating DESC;
```



