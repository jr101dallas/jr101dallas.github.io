---
layout: post
title:  "Gone Fishing"
date:   2021-12-06 09:11:34 +0000
category: detour
tags: code sql fun
---

# Skipping
I skipped [Advent of Code](https://adventofcode.com/2021) Day Five for now. I didn't immediately have a strategy for the solution and looked ahead to Day Six since I was behind a bit. Good News! Going Fishing!  

## Data Modeling
Data transformations are super easy in SQL. You just need to set up the relationships. For instance, here are the table representations of the fish population, fish lifecycle, and fish reproduction.  

``` sql
select *
into #population
from (
	values(3),
    (5),
    ...
    (4)
) t(fish)

select *
into #lifecycle
from (
	VALUES(8, 7, 0),
	(7, 6, 0),
	(6, 5, 0),
	(5, 4, 0),
	(4, 3, 0),
	(3, 2, 0),
	(2, 1, 0),
	(1, 0, 0),
	(0, 6, 1)
) t(age, nextAge, newFish)

select *
into #newfish
from (
	values(1, 8),
	(1, 6)
) t(newfish, age)
```

With those quick data models, the solution to both puzzle one and puzzle two look like this.  

``` sql
DECLARE @todayPop TableRowNumAndBigInt,
	@tomorrowPop TableRowNumAndBigInt,
	@days BIGINT = 255

INSERT INTO @tomorrowPop (rowNum, val)
SELECT COALESCE(oa.age, l.nextAge), COUNT(fish)
FROM #population p
INNER JOIN #lifecycle l ON p.fish = l.age
OUTER APPLY(SELECT age FROM #newfish n WHERE n.newfish = l.newFish) oa
GROUP BY fish, l.nextAge, oa.age

WHILE @days > 0
BEGIN
	INSERT INTO @todayPop
	SELECT * FROM @tomorrowPop

	DELETE @tomorrowPop

	INSERT INTO @tomorrowPop (rowNum, val)
	SELECT COALESCE(oa.age, l.nextAge), SUM(val)
	FROM @todayPop p
	INNER JOIN #lifecycle l ON p.rowNum = l.age
	OUTER APPLY(SELECT age FROM #newfish n WHERE n.newfish = l.newFish) oa
	GROUP BY rowNum, l.nextAge, oa.age

	SELECT @days = @days - 1
	DELETE @todayPop
END


SELECT *
FROM @tomorrowPop

SELECT SUM(val)
FROM @tomorrowPop
```

As a quick reminder, the custom type used above just looks like this.  

``` sql
CREATE TYPE TableRowNumAndBigInt AS TABLE ( rowNum BIGINT, val BIGINT );
GO
```

## Watch Out
The count limiter above is 255 for the 256 fish generations, and was 79 for the 80 generations. Also, I didn't originally do the group by and sum, and had a straight fish population to new fish population select in the middle. It ran for 7 hours while I slept before I cancelled it....  
