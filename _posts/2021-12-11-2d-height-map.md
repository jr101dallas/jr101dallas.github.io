---
layout: post
title:  "2d Height Map"
date:   2021-12-11 09:11:34 +0000
category: detour
tags: code sql fun
---

# Ooch!
The data for this one is a 100 x 100 height map. There is no way I'm doing this as a 100 column table. That would be a pain as well as awkward and ludicrous. But, now I have to figure out what I am going to do. Really, I think where I need to go is a three-ish column table of x, y, height so that I can do some windowing function magic. I'm not positive that's a perfect way to go, because there's so logic there that I'm not 100% sure how to pull off, but I've definitely got to get the data parsed and into the database first. I could cheat and say that the SQL doesn't start till the data is in the database (I did already do some of that with some regex data formatting) but I'm really aiming for actual SQL solutions so I'm going to try avoiding that.  

## Data Processing
So, if I want to set this up for an arbitrarily large data set, which for at least some of the puzzles I have definitely only engineered a solution for the specific size of the set, then I would need to do something like the following.  

1. assuming possibly very large rows, I would have to plan for chunking, so I need a row number value independent of the identity column
1. I still need identity for sort within that row number grouping unless I create an independent row segment number
1. I could go really big chunks with NVARCHAR(MAX) but might have to decide that after I plan out the row parsing logic
1. row parsing would need to create output of row number, easy from the field in the table, character number which isn't as easy, and the value of the character

Working backwards then, my data insert is really easy if I can figure out the row parsing. That's where to start then.  

``` sql
CREATE OR ALTER FUNCTION LineParser(@Row INT, @RowColumnString VARCHAR(MAX))
RETURNS TABLE
RETURN
WITH input AS (
	SELECT LEN(@RowColumnString) AS charLen
)
, limiter AS (
	SELECT val AS col
	FROM Numbers n
	WHERE n.val <= (SELECT charLen FROM input)
)
SELECT 
	ROW_NUMBER() OVER(ORDER BY (SELECT NULL)) AS GroupIdentity,
	@Row AS MapRowNum,
	ca.CharAtCol
FROM limiter l
CROSS APPLY (SELECT SUBSTRING (@RowColumnString, l.col , 1 ) AS CharAtCol) ca
GO

SELECT * 
FROM dbo.LineParser(3, '6543467789865478999899865439123998965323234569549878542987632459976989457993567987542196569879210145')
```

That was easier than I was thinking, now I just have to insert data and then transform it into my working table.  

``` sql
CREATE TABLE HeightMapRaw (id BIGINT IDENTITY, MapRow VARCHAR(100))

INSERT INTO HeightMapRaw (MapRow)
SELECT MapRow
FROM (
VALUES('6769876887698999876367898543212378997654321291098765432398767667989976543210123456987678999766598921'),
('5456965476567999985457897654301456989743210989989898540987656545678987654323634679876569998757387892'),
...
('4345896595432124588998765545987674567899999876345987658987659767896431239109876543456789432101299965')
) t(MapRow)

SELECT 
	ROW_NUMBER() OVER(ORDER BY m.id, ca.GroupIdentity) AS DatumId,
	m.id AS MapRow,
	ca.GroupIdentity AS MapColumn,
	ca.CharAtCol AS Height
INTO HeightMap
FROM HeightMapRaw m
CROSS APPLY dbo.LineParser(m.id, m.MapRow) ca
```

Now I have a table HeightMap with my x, y coordinates and height values that I should be able to use my windoing functions on. There might be some funny business around getting only the orthogonal map coordinates but I'm not completely sure yet.  

## Puzzle One
Finding the local minima works out to the following query. I had to add the maximimum calculations because I originally wasn't accounting for a totally flat three by three area (really in this case a flat plus sign) or part of one on the edge, and I have a blocks of nines like that in my data. The example didn't really say what to do in a case like that, but the answer I tried first was too high and so I went looking for edge cases.  

``` sql
WITH calculateLocalMins AS (
	SELECT *, 
		MIN(Height) OVER(PARTITION BY MapColumn ORDER BY MapRow ROWS BETWEEN 1 PRECEDING AND 1 FOLLOWING ) AS LocalMinRow, 
		MIN(Height) OVER(PARTITION BY MapRow ORDER BY MapColumn ROWS BETWEEN 1 PRECEDING AND 1 FOLLOWING ) AS LocalMinColumn, 
		MAX(Height) OVER(PARTITION BY MapColumn ORDER BY MapRow ROWS BETWEEN 1 PRECEDING AND 1 FOLLOWING ) AS LocalMaxRow, 
		MAX(Height) OVER(PARTITION BY MapRow ORDER BY MapColumn ROWS BETWEEN 1 PRECEDING AND 1 FOLLOWING ) AS LocalMaxColumn
	FROM HeightMap
)
, filterLocalMins AS (
SELECT *, Height + 1 AS RiskFactor 
FROM calculateLocalMins
WHERE LocalMinRow = Height
	AND LocalMinColumn = Height
	AND LocalMaxRow != Height
	AND LocalMaxColumn != Height
)
SELECT SUM(RiskFactor)
FROM filterLocalMins
```

## Puzzle Two
Locating basins. Uh oh. I'm not sure my code is set up for this. Nines don't count as part of a basin, and the contiguous group of numbers surrounded by nines is a basin. Find the three largest basins and the count of the locations in them, and multiply the three together.  

First, I'm thinking that maybe I should just go looking on google for ways to tackle this problem. Second, I'm thinking that the easy visual identification method that keeps running through my head is getting in the way of thinking of a decent programmatic way of doing it.  

First lets think of how to do it at all and then later I'll figure out if or how to do it in SQL. If I start with finding pieces of a basin in a row, I can keep track of the position in the row and create a new array of locations when I find a non-nine value. I add the position of that value to the new array and move on to the next position in the row. If it is also non-nine, I keep adding the positions to the array. When I hit a nine I know to close that array for now and I wait for the next non-nine to create the next array.  

Between rows, when I find a non-nine value I can check the current position in the row against the same position in the previous row to see if this basin is part of a previously encountered basin. For a case where the basin is narrower in the current row thsn the last row, i.e the first position of the basin in the previous row is in a position further along the row than the first position in this row, I'll have already started a new basin array and I'll need to be able to merge my new array into the previously existing basin. It would look something like this.  

```
9993456
9876567
```

While processing the first row, I create a basin array when I hit the three in the fourth position. Then, while processing the second row, I hit the eight in the second position, check if there's a basin to connect it to in the previous row, decide there isn't, and start a second basin. But then when I get to the fourth position, still working on adding to my second basin, I discover that this is actually the same basin as a previous one and I need to merge them.  

All of that said, I think I have a way forward at least RBAR even if I can't find a way to do this regular database-wise.  

Nope. Stuck and thinking about it. I've got an answer, but it's wrong and I'm going to have to start looking for where the bug is or switch languages again. I'll skip to the other puzzles for a while and see if I can't come back to this later.  