---
layout: post
title:  "Debugging"
date:   2021-12-04 09:11:34 +0000
category: detour
tags: code sql fun c# tests
---

# Cases
Had a little space not to work on this and then spent some time learning bitwise operators and bitmasking logic in C#, but finally back on this. I'm pretty confident in my SQL skills, so I was half ready to believe there was a problem with the data. So, I switched to C# where my unit testing is stronger and started from the bottom. You've seen the tests I've been writing for my roguelike project, I've got tests from the bottom. It's a good thing too, because I was using some logic incorrectly in the bitmasking for sure.  

I got the C# solution working and got a confirmation on the correct answer from Advent of Code, so now I know 100% sure it's a bug in my SQL. So, I'm going to try breaking things up a bit to see if I can find the bug. Get ready to find that I'm doing a > instead of a >= or something similar. I'm also a little tempted to write my integers to a file and use those to insert into a SQL table so that I can use my bitmasking logic in SQL too. We'll see.  

## Cleaning
First, a little cleanup to pretty up my puzzle one code to just "give the answer" to copy and paste. I switched to a global temp table because I
m breaking things up into multiple query windows (multiple connections) and that way I can hit my table fine.  

``` sql
DECLARE @high INT, @low INT;

WITH sums AS (
SELECT 
	CASE WHEN SUM(a) > 500 THEN 1 ELSE 0 END * POWER(2,11) AS a, 
	CASE WHEN SUM(b) > 500 THEN 1 ELSE 0 END * POWER(2,10) AS b, 
	CASE WHEN SUM(c) > 500 THEN 1 ELSE 0 END * POWER(2,9) AS c, 
	CASE WHEN SUM(d) > 500 THEN 1 ELSE 0 END * POWER(2,8) AS d, 
	CASE WHEN SUM(e) > 500 THEN 1 ELSE 0 END * POWER(2,7) AS e, 
	CASE WHEN SUM(f) > 500 THEN 1 ELSE 0 END * POWER(2,6) AS f, 
	CASE WHEN SUM(g) > 500 THEN 1 ELSE 0 END * POWER(2,5) AS g, 
	CASE WHEN SUM(h) > 500 THEN 1 ELSE 0 END * POWER(2,4) AS h, 
	CASE WHEN SUM(i) > 500 THEN 1 ELSE 0 END * POWER(2,3) AS i, 
	CASE WHEN SUM(j) > 500 THEN 1 ELSE 0 END * POWER(2,2) AS j, 
	CASE WHEN SUM(k) > 500 THEN 1 ELSE 0 END * POWER(2,1) AS k, 
	CASE WHEN SUM(l) > 500 THEN 1 ELSE 0 END * POWER(2,0) AS l
FROM ##bits
)
SELECT @high = s.a + s.b + s.c + s.d + s.e + s.f + s.g + s.h + s.i + s.j + s.k + s.l 
FROM sums s;

WITH sums AS (
SELECT 
	CASE WHEN SUM(a) > 500 THEN 0 ELSE 1 END * POWER(2,11) AS a, 
	CASE WHEN SUM(b) > 500 THEN 0 ELSE 1 END * POWER(2,10) AS b, 
	CASE WHEN SUM(c) > 500 THEN 0 ELSE 1 END * POWER(2,9) AS c, 
	CASE WHEN SUM(d) > 500 THEN 0 ELSE 1 END * POWER(2,8) AS d, 
	CASE WHEN SUM(e) > 500 THEN 0 ELSE 1 END * POWER(2,7) AS e, 
	CASE WHEN SUM(f) > 500 THEN 0 ELSE 1 END * POWER(2,6) AS f, 
	CASE WHEN SUM(g) > 500 THEN 0 ELSE 1 END * POWER(2,5) AS g, 
	CASE WHEN SUM(h) > 500 THEN 0 ELSE 1 END * POWER(2,4) AS h, 
	CASE WHEN SUM(i) > 500 THEN 0 ELSE 1 END * POWER(2,3) AS i, 
	CASE WHEN SUM(j) > 500 THEN 0 ELSE 1 END * POWER(2,2) AS j, 
	CASE WHEN SUM(k) > 500 THEN 0 ELSE 1 END * POWER(2,1) AS k, 
	CASE WHEN SUM(l) > 500 THEN 0 ELSE 1 END * POWER(2,0) AS l
FROM ##bits
)
SELECT @low = s.a + s.b + s.c + s.d + s.e + s.f + s.g + s.h + s.i + s.j + s.k + s.l 
FROM sums s;

select @high * @low AS DayThreeAnswerOne;
```

## Puzzle Two
Ok, smaller steps, I'm not as smart as I think.  

Walking through the pieces and breaking out functions and procedures, I might have discovered one problem. This doesn't work out the same if the number data types are BIGINT and I'm pretty sure I've been using that everywhere. I wasn't thinking of the case of an odd number of items and a .5 getting dropped.  

``` sql
CREATE OR ALTER FUNCTION ZeroOrOne(@ReturnMost BIT, @count DECIMAL(19,2), @sum DECIMAL(19,2))
RETURNS TINYINT
BEGIN
	DECLARE @ZeroOrOne TINYINT;
	IF (@ReturnMost = 1)
	BEGIN
		SET @ZeroOrOne = CASE WHEN (@sum >= @count/2) THEN 1 ELSE 0 END; 
	END
	ELSE
	BEGIN
		SET @ZeroOrOne = CASE WHEN (@sum >= @count/2) THEN 0 ELSE 1 END; 
	END

	RETURN @ZeroOrOne;
END
GO

select *, dbo.ZeroOrOne(c.OnOrOff,t.cnt,t.num)
from (
	values(100,50),
	(51,25),
	(51,26)
) t(cnt,num)
cross join (
	values(0),
	(1)
) c(OnOrOff)
```

Ugh! It's always the little things. I went back to the code from yesterday's post and swapped out the TINNYINT types in the DECLARE statment at the top and got the right answers. But, I guess I can solve the puzzle in either C# or SQL now.  