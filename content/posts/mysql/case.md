---
title: "mysql中的case用法"
date: 2020-09-22T20:52:24+08:00
draft: false
---

最近遇到一个问题：

| year | amount | number |
|--- |--- |--- |
| 1991 | 1 | 1.1 |
| 1991 | 2 | 1.2 |
| 1991 | 3 | 1.3 |
| 1992 | 1 | 1.1 |
| 1992 | 2 | 1.2 |
| 1992 | 3 | 1.3 |

把上面表格的数据查询成：

| year | m1 | m2 | m3 |
|--- |--- |--- |--- |
| 1991 | 1.1 | 1.2 | 1.3 |
| 1992 | 2.1 | 2.2 | 2.3 |

看到这样的需求，首先想到的是用case去统计以及 用group by来分组

第一版sql代码：

```sql
SELECT
	`year`,
	(CASE WHEN amount = 1 THEN num END) AS m1,
	(CASE WHEN amount = 2 THEN num END) AS m2,
	(CASE WHEN amount = 3 THEN num END) AS m3
FROM
	test
GROUP BY
	`year`
```

查询出来的结果有点不如人意：

| year | m1 | m2 | m3 |
|--- |--- |--- |--- |
| 1991 | 1.1 |  |  |
| 1992 | 2.1 |  |  |

这么说明了分组之后只显示到第一行数据，那么我们去掉分组看看：

```sql
SELECT
	`year`,
	(CASE WHEN amount = 1 THEN num END) AS m1,
	(CASE WHEN amount = 2 THEN num END) AS m2,
	(CASE WHEN amount = 3 THEN num END) AS m3
FROM
	test
```

| year | m1 | m2 | m3 |
|--- |--- |--- |--- |
| 1991 | 1.1 | 0 | 0 |
| 1991 | 0 | 1.2 | 0 |
| 1991 | 0 | 0 | 1.3 |
| 1992 | 2.1 | 0 | 0 |
| 1992 | 0 | 2.2 | 0 |
| 1992 | 0 | 0 | 2.3 |

有点像我们想要的了，只是没有分组以及去掉空值

而且我们可以看出，在分组的情况下m1,m2,m3的值都是一个最大值来的

所以我们可以用一个子查询来查询上面的结果集中分组的最大值 最终版sql：

```sql
SELECT
	`year`,
	MAX(n1) AS m1,
	MAX(n2) AS m2,
	MAX(n3) AS m3
FROM
	(
		SELECT
			`year`,
			(CASE WHEN amount = 1 THEN num END) AS m1,
			(CASE WHEN amount = 2 THEN num END) AS m2,
			(CASE WHEN amount = 3 THEN num END) AS m3
		FROM
			test
	) AS a
GROUP BY
	`year`
```

最终可以得到我们想要的结果：

| year | m1 | m2 | m3 |
|--- |--- |--- |--- |
| 1991 | 1.1 | 1.2 | 1.3 |
| 1992 | 2.1 | 2.2 | 2.3 |