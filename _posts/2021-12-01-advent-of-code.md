---
layout: post
title:  "Advent of Code"
date:   2021-12-01 09:11:34 +0000
category: detour
tags: code sql fun
---

# Detour
A friend of mine at work clued me in to [Advent of Code](https://adventofcode.com/2021) and I'm taking a little detour on it! I started out on the first puzzle of the first day in Powershell, just for fast work and I alread had a script that was almost what I needed. But, I hit the second puzzle and realized it was a ton easier in SQL (at least t-sql SQL Server) so I went back and retrofit the first puzzle that way too.  

## Setup
So the first puzzle started right off with a sequence of values, which often screams data processing to me, but the task at hand was a little too simple. The initial task is just to get a count of when the sequence increases from the previous value. Procedural, loop the values once and keep track of the previous one, compare previous to current and increment a counter when it's greater. Totally simple, that was why I started with Powershell.  

The SQL way is as easy for the first puzzle. First load a table, 'cause SQL. This does a couple of things. First, it gives us easy sort on our sequence by the ```IDENTITY``` column in case we need to do something squirrely later. Second, it gives us easy repeatability to mess around with different ```SELECT``` statements on it later. Note that I'm creating and inserting into a local temp table, scope is the connection so it cleans up when I shut down the tab in SSMS.  

``` sql 
CREATE TABLE #measurements (rowNumber BIGINT IDENTITY, depth BIGINT);

INSERT INTO #measurements (depth)
SELECT t.depth
from (
	VALUES(190),
        (168),
        ...
        (4482)
) t(depth);
```

The first puzzle then, retrofit to SQL, gets a solution like this. A Common Table Expression (CTE) lets us set up a few columns so we can easily see what's happening. Once you take a look, you'll probably see why the SQL solution wasn't the first one I went to. This is technically a tricky use of a degenerate case for a windowing function. The window in the PreviousValue column is from the preceding row to the preceding row. Likewise, CurrentValue has a window of the current row to the current row. Summing the windows of values trivially returns the value itself. That makes DiffOfValues a simple subtraction of the preceding value from the current value. 

``` sql
WITH cte AS (
    SELECT rowNumber, depth, 
        SUM(depth) OVER(ORDER BY rowNumber ROWS BETWEEN 1 PRECEDING AND 1 PRECEDING) AS PreviousValue,
        SUM(depth) OVER(ORDER BY rowNumber ROWS BETWEEN CURRENT ROW AND CURRENT ROW) AS CurrentValue,
        SUM(depth) OVER(ORDER BY rowNumber ROWS BETWEEN CURRENT ROW AND CURRENT ROW) 
            - SUM(depth) OVER(ORDER BY rowNumber ROWS BETWEEN 1 PRECEDING AND 1 PRECEDING) AS DiffOfValues
    FROM #measurements
)
SELECT 
	COUNT(CASE WHEN DiffOfValues = 0 THEN DiffOfValues ELSE NULL END) AS Zeroes,
	COUNT(CASE WHEN DiffOfValues > 0 THEN DiffOfValues ELSE NULL END) AS Increases,
	COUNT(CASE WHEN DiffOfValues < 0 THEN DiffOfValues ELSE NULL END) AS Decreases
FROM cte
```

Once I have the sequence of differences, I select from the CTE to split out my cases and count them.  

## Backwards
The degenerate case and the convoluted thinking of that solution, even though fairly simple, was much easier to get to from the solution to the second puzzle. Working backwards is sometimes way easier. The setup for the second puzzle actually suggests a rolling window, a classic use case for windowing functions, in this case to smooth out the data curve and see the trend in depth better. That solution looks much like the one above but more "normal looking" for the windowing functions.  

``` sql
WITH cte AS (
    SELECT rowNumber, depth, 
        SUM(depth) OVER(ORDER BY rowNumber ROWS BETWEEN 2 PRECEDING AND CURRENT ROW) AS SumTwoBefore,
        SUM(depth) OVER(ORDER BY rowNumber ROWS BETWEEN 1 PRECEDING AND 1 FOLLOWING) AS SumBeforeAndAfter,
        SUM(depth) OVER(ORDER BY rowNumber ROWS BETWEEN 1 PRECEDING AND 1 FOLLOWING) 
            - SUM(depth) OVER(ORDER BY rowNumber ROWS BETWEEN 2 PRECEDING AND CURRENT ROW) AS DiffOfSums
    FROM #measurements
)
, detection AS (
    select *, 
        CASE WHEN DiffOfSums = 0 THEN 'no change' ELSE NULL END AS Zeroes,
        CASE WHEN DiffOfSums > 0 THEN 'increased' ELSE NULL END AS Increases,
        CASE WHEN DiffOfSums < 0 THEN 'decreased' ELSE NULL END AS Decreases
    from cte 
    where rwnm between 3 AND 1999
)
select COUNT(Zeroes) as Zeroes, COUNT(Increases) as Increases, COUNT(Decreases) as Decreases, COUNT(0) AS AllOfThem
from detection
```

Here we can see the normal windowing, two rows preceding to the current row and one row preceding to one row following. The subtraction is the same. The second CTE is where things get slightly more interesting, we need a where clause to exclude the rows where the calculation is invalid, the first two rows and the final row. In the previous puzzle, this also collapses to a simpler case and I directly nest the case statements in the count statements.  

And that was day one.  