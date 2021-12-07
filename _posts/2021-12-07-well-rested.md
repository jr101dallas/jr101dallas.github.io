---
layout: post
title:  "Well Rested"
date:   2021-12-07 17:11:34 +0000
category: detour
tags: code sql fun
---

# Rethinking
Day Five looked like nonsense to implement when I looked at it late last night. But, I just had a flash of how to do it easily. It starts with just loading the data like this.  

``` sql 
SELECT *
INTO EndpointPairs
FROM (
    VALUES(629,581,123,75),
    (921,643,452,643),
    ...
    (160,751,787,124)
) t(x1,y1,x2,y2)
```

## Fun Times
Identifying the orthogonal lines is as simple as this.  

``` sql
SELECT * 
FROM EndpointPairs
WHERE x1 = x2 or y1 = y2
```

But I need to fill in all the points on the line segments. A join across a numbers or values table will make tha really easy, so here's that.  

``` sql 
SELECT ROW_NUMBER() OVER(ORDER BY t.val) AS val
INTO Numbers
FROM (
	VALUES(1),(2),(3),(4),(5),(6),(7),(8),(9),(10)
) t(val)
cross join (
	VALUES(1),(2),(3),(4),(5),(6),(7),(8),(9),(10)
) c1(val)
cross join (
	VALUES(1),(2),(3),(4),(5),(6),(7),(8),(9),(10)
) c2(val) 
cross join (
	VALUES(1),(2),(3),(4),(5),(6),(7),(8),(9),(10)
) c3(val)
```

Now the real plan is just to fill a table of points and do a group by the ordered pair, with a having for the at least two part. To get there, I need to expand the endpoints into the full list of points to insert into my points table. That sounds to me like a table valued function with three parameters, so here's that.  

``` sql 
CREATE OR ALTER FUNCTION LineSegmentFromInterceptAndRange(@intercept BIGINT, @r1 BIGINT, @r2 BIGINT)
RETURNS TABLE
RETURN
WITH limits AS (
	SELECT MAX(ends) AS mx, MIN(ends) AS mn
	FROM (
		VALUES(@r1),
		(@r2)
	) v(ends)
)
SELECT @intercept AS intercept, n.val AS rng
FROM Numbers n
CROSS APPLY(SELECT * FROM limits) ca
WHERE n.val BETWEEN ca.mn AND ca.mx
GO

SELECT *
FROM dbo.LineSegmentFromInterceptAndRange(5,18,30)
```

## TRANSFOOOOORM!!!!!!
Now I can load my table. No looping, this is SQL.    

``` sql 
SELECT ca.intercept as x, ca.rng as y
INTO PointList
FROM EndpointPairs p
CROSS APPLY dbo.LineSegmentFromInterceptAndRange(p.x1, p.y1, p.y2) ca
WHERE x1 = x2


INSERT INTO PointList
SELECT ca.rng as x, ca.intercept as y
FROM EndpointPairs p
CROSS APPLY dbo.LineSegmentFromInterceptAndRange(p.y1, p.x1, p.x2) ca
WHERE y1 = y2
```

Ok, now it's just a matter of my select because the rowcount will be may answer.  

``` sql
SELECT x,y,COUNT(0) AS cnt
FROM PointList l
GROUP BY x,y
HAVING COUNT(0) > 1
```

## Puzzle Two
Hah, if I can figure out how to load my data, I'm finished. Unfortunately, that means I need to think a bit, and probably look at my data. I'm isolating my diagonals first.  

``` sql
SELECT *
INTO Diagonals
FROM EndpointPairs

DELETE Diagonals WHERE x1 = x2
DELETE Diagonals WHERE y1 = y2
```

Before I was thinking of my function as a slope intercept idea with either slope zero or infinite/undefined. A 45 degree slope is easy with slope intercept too. Either it's x=y or it's x=-y and add the intercept. Let me go get a quick refresher on finding that and I'll not have to reinvent it. So that makes my function and loading my table look like this.  

``` sql
CREATE OR ALTER FUNCTION LineSegmentFromTwoPoints(@x1 DECIMAL(10,2), @y1 DECIMAL(10,2), @x2 DECIMAL(10,2), @y2 DECIMAL(10,2))
RETURNS TABLE
RETURN
WITH limits AS (
	SELECT MAX(ends) AS mx, MIN(ends) AS mn
	FROM (
		VALUES(@x1),
		(@x2)
	) v(ends)
)
, slope AS (
	SELECT (@y2 - @y1) / (@x2 - @x1) AS m
)
, intercept AS (
	SELECT @y1 - ((SELECT m FROM slope) * @x1) AS b
)
SELECT n.val AS x, ((n.val * (SELECT m FROM slope)) + (SELECT b FROM intercept)) AS y
FROM Numbers n
CROSS APPLY(SELECT * FROM limits) ca
WHERE n.val BETWEEN ca.mn AND ca.mx
GO

INSERT INTO PointList
SELECT ca.x, ca.y
FROM Diagonals d
CROSS APPLY dbo.LineSegmentFromTwoPoints(d.x1, d.y1, d.x2, d.y2) ca
```

And now, I run my same summarization query again.  

``` sql 
SELECT x,y,COUNT(0) AS cnt
FROM PointList l
GROUP BY x,y
HAVING COUNT(0) > 1
```

And that is that.  