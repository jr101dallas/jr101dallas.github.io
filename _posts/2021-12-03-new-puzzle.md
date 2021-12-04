---
layout: post
title:  "New Puzzle"
date:   2021-12-03 09:11:34 +0000
category: detour
tags: code sql fun
---

# Hooked
I admit, I'm having fun with these puzzles. This one looks a little different on the surface. In fact, at first glance, I'm wondering if I can just do the bitwise operations and have it come out right. I don't use bitwise operations much so I need to go look it up, but also, I'm a little suspicious about what the twist is going to be for the second puzzle. If the second puzzle goes from binary to ternary, or something else odd, then I'll be really unhappy with clever shortcuts.  

## Binary
I forgot about there not being a nice easy way to get a binary string of ones and zeroes into a real binary value so I went a little different way with the solution to the first puzzle. I just split the initial data set into a column per digit. That's not a big deal for me since I'm good with regex but I might not have wanted to do it that way if the data input was more friendly to SQL.  

``` sql 
CREATE TABLE #bits
(rowNumber BIGINT IDENTITY, a TINYINT, b TINYINT, c TINYINT, d TINYINT, e TINYINT, f TINYINT, g TINYINT, h TINYINT, i TINYINT, j TINYINT, k TINYINT, l TINYINT)

INSERT INTO #bits (a, b, c, d, e, f, g, h, i, j, k, l)
SELECT *
FROM (
VALUES(1,1,0,0,0,1,0,1,0,1,1,0),
	(0,1,1,1,0,1,1,1,1,1,0,1),
    ...

	(0,1,1,1,0,0,0,1,1,0,1,0)
) t(a, b, c, d, e, f, g, h, i, j, k, l)
```

This makes the solution to the first puzzle pretty simple. This query will give all the binary places that need to be added together. I did the adding with a copy of the result and putting plusses between and a select in front, but I could have turned around and declared variables, set them in the select, and then selected the addition of the variables all together. The other half of the first solution is gained by swapping the zero and one in the then/else and repeating the addition. I didn't close the loop on the pretty closure for the math because I was interested in seeing what the second puzzle was.  

``` sql
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
FROM #bits
```

## Oof
Ok, the second puzzle was going to be super simple. But, I read it wrong. I thought I was going to end up with where and, and, and.... Not so. The puzzle doesn't go for subset of a subset of a subset. It wants a fresh calculation of the bit place each iteration. I feel certain there's a smarter way to do it, but I've got a solution.  

``` sql
DECLARE @loop TINYINT = 2, --to skip rowNumber
	@currentHighCount SMALLINT,
	@currentRowCount SMALLINT,
	@sql NVARCHAR(1000),
	@RowSet TableOfBigIntegers,
	@ParmDefinition NVARCHAR(1000) = N'@rows TableOfBigIntegers READONLY';

--First, something to loop over, 'cause SQL
SELECT * INTO #cols FROM sys.dm_exec_describe_first_result_set(N'SELECT TOP 1 * FROM #bits;', null, 0)

--Second initialize the first set limiter
INSERT INTO @RowSet SELECT rowNumber FROM #bits

WHILE EXISTS(SELECT * FROM #cols WHERE column_ordinal = @loop)
BEGIN
	--alias the values in the columns to 'val' for reusable code and limit the rows by @RowSet
	SELECT @sql = N'SELECT rowNumber, ' + name + ' AS val 
					INTO ##workingSet 
					FROM #bits b 
					INNER JOIN @rows r ON b.rowNumber = r.val' 
	FROM #cols 
	WHERE column_ordinal = @loop 
	
	EXEC sp_executesql @sql, @ParmDefinition, @rows = @RowSet;

	--get the count of rows so I know which is more
	SELECT @currentRowCount = COUNT(0) FROM ##workingSet
	
	--get the number of ones by summing and pick which is most prevalent by more than half the count or not
	SELECT @currentHighCount = CASE WHEN SUM(val) >= @currentRowCount/2 THEN 0 ELSE 1 END	
	FROM  ##workingSet b

	--empty and reload the row limiter
	DELETE @RowSet
	INSERT INTO @RowSet SELECT rowNumber FROM ##workingSet WHERE val = @currentHighCount

	IF @@ROWCOUNT <= 1
	BEGIN
		SELECT * FROM #bits b INNER JOIN @RowSet r ON b.rowNumber = r.val
		BREAK
	END

	SET @loop = @loop + 1
	DROP TABLE ##workingSet
END

DROP TABLE #cols
```

Er, well, I almost have a solution. I have a bug somewhere because I'm not getting the right answer. I get single rows of bits for both of the pieces (swap the 1 and 0 for the other) and I put together a fast multiplier/summer. But it's not right yet. Maybe tomorrow, sleep now.  

``` sql
SELECT sum(num)
FROM (
VALUES
	(1 * POWER(2,11)), 
	(1 * POWER(2,10)), 
	(0 * POWER(2,9) ), 
	(0 * POWER(2,8) ), 
	(1 * POWER(2,7) ), 
	(1 * POWER(2,6) ), 
	(1 * POWER(2,5) ), 
	(1 * POWER(2,4) ), 
	(1 * POWER(2,3) ), 
	(1 * POWER(2,2) ), 
	(1 * POWER(2,1) ), 
	(1 * POWER(2,0) )
) t(num)
```