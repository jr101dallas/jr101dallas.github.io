---
layout: post
title:  "Crab Alignment"
date:   2021-12-08 09:11:34 +0000
category: detour
tags: code sql fun
---

# What?
Crab alignment is the artiface of the puzzle; sure, why not, bingo games with a giant squid came earlier.  

## Optimization
I'm thinking that this is a calculus problem. I have to think again about it but I think that's what it is. Look [here](https://adventofcode.com/2021/day/7) if you want.  

Think SQL. First load the data.  

``` sql 
SELECT *
INTO CrabAlignment
FROM (
    VALUES(1101),
    (1),
    (29),
    ...
    (274)
) t(position)
```

Now it's actually just a query and the top row is the answer with the rest of the solutions below.  

``` sql
WITH num AS (
	SELECT *
	FROM numbers 
	WHERE val < 1950
)
, expansion AS (
	SELECT c.position, n.val AS alignment, ABS(c.position - n.val) AS fuel
	FROM CrabAlignment c
	CROSS JOIN num n
)
SELECT e.alignment, SUM(fuel) AS TotalFuel
FROM expansion e
GROUP BY e.alignment
ORDER BY SUM(fuel)
```

# Nonlinear Optimization
Puzzle two requires a translation table for fuel cost versus distance, luckily it's a simple to implement formula. The query changes some, but it's still a "single" somewhat complex query.  

``` sql
WITH num AS (
	SELECT *, SUM(val) OVER(ORDER BY val) AS xFuel
	FROM numbers 
	WHERE val < 1950
)
, expansion AS (
	SELECT c.position, n.val AS alignment, ABS(c.position - n.val) AS fuel
	FROM CrabAlignment c
	CROSS JOIN num n
)
, expensive AS (
	SELECT e.*, n.xFuel
	FROM expansion e
	INNER JOIN num n ON e.fuel = n.val
)
SELECT e.alignment, SUM(xFuel) AS TotalFuel
FROM expensive e
GROUP BY e.alignment
ORDER BY SUM(xFuel)
```

The first row shows the best position for alignment and the total fuel spent.  
