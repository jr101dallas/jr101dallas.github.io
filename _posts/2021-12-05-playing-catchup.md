---
layout: post
title:  "Playing Catchup"
date:   2021-12-05 09:11:34 +0000
category: detour
tags: code sql fun
---

# Behind On Puzzles
After totally switching gears and solving Day Three in two different languages because I was not seeing a bug in my SQL data types, I'm ready to try making some rapid progress on puzzles but I think I'm looking at more of a data pipeline problem than anything else in Day Four.  

The puzzle is presented as bingo cards, and the first question is which will win.  

## Data
Before I was doing some regex replace formatting of the input data just to make the insert statements easy, but now with two data sets in the same page/link I definitely don't mind messing around with the data in some preprocessing steps. Sometimes that just saves a ton of code. But, in this case I'm thinking that the preprocessing may be extensive enough that I need to include that logic in my solution.  

Data set one is a sequence of numbers to be called by the bingo caller. It's just the first line so I'm manually copying that into a separate file. I'm working in vscode, so regular expression (regex) flavors may vary but a regex replace of ```(\d+),?``` with ```($1),\n``` gives me a nice result to just paste into the body of my VALUES atatement for my insert.  

Data set two is lots more fun. We've got 5 x 5 grids of numbers with blank lines between. I'm hesitating here because what I have in mind would be super simple in C# by just incrementing a counter whenver I see a blank line in my file processing loop. Actually.... LOL. It's been a long time since I've gotten to be creative with calculated columns. Technically, table definitions are a lot like objects and class definitions. I had to do a little testing to make sure I had the edge cases right but I'm going to just do a straight insert of the bingo board rows into this table.  

``` sql
CREATE TABLE BingoBoards (rowNumber BIGINT IDENTITY(0,1), 
	board AS (rowNumber + 1 - (rowNumber % 5))/5, 
	boardRow AS ((rowNumber % 5) + 1), 
	b BIGINT, 
	i BIGINT, 
	n BIGINT, 
	g BIGINT, 
	o BIGINT)
```

And that will give me row ordering if I need it and it gives me board numbering, both are 0 based the way I set it up. That means that my preprocessing is just this regex replace of ```^\s?(\d{1,2})\s{1,2}(\d{1,2})\s{1,2}(\d{1,2})\s{1,2}(\d{1,2})\s{1,2}(\d{1,2})``` with ```($1,$2,$3,$4,$5),``` to get the insert nicely formatted.  

Now that I have a table of rows, I need a table of columns as we so I can check both directions easily. I'm not thrilled with this answer, but there are five of these.  

``` sql
CREATE OR ALTER FUNCTION TransposeB(@boardNum BIGINT)
RETURNS TABLE
RETURN
SELECT    
  [1] AS b, [2] AS i, [3] AS n, [4] AS g, [5] AS o
FROM  
(
  SELECT boardRow, b 
  FROM bingoboards
  WHERE board = @boardNum
) AS SourceTable  
PIVOT  
(  
  SUM(b)  
  FOR boardRow IN ([1], [2], [3], [4], [5])  
) AS PivotTable;
GO
```

With those I don't have to do dynamic SQL to do this even though it's redundant. Now at least I can loop and transform my columns to rows for my query.  

``` sql
DECLARE @board BIGINT = 0;

WHILE @board < 100
BEGIN
	INSERT INTO BingoBoardsCols (b, i, n, g, o)
	SELECT b, i, n, g, o
	FROM (
	SELECT 1 AS sort, * FROM dbo.TransposeB(@board)
	UNION
	SELECT 2 AS sort, * FROM dbo.TransposeI(@board)
	UNION
	SELECT 3 AS sort, * FROM dbo.TransposeN(@board)
	UNION
	SELECT 4 AS sort, * FROM dbo.TransposeG(@board)
	UNION
	SELECT 5 AS sort, * FROM dbo.TransposeO(@board)
	) t(sort, b, i, n, g, o)
	ORDER BY sort

	SELECT @board = @board + 1
END
```

## Query
Now that I've got my data easy, there's just a little more to do. I insert five of these into a temp table. The b.b has to be changed for the other four, b.i, b.n, b.g, b.o. Then select the temp table ordered by LastSequence and first row is the board that wins first and the sequence number it's called on. Note the sequence number!  

``` sql
SELECT TOP 5 b.RowOrCol, b.board, MAX(s.rowNumber) LastSequence
FROM BingoRowsAndColumns b
INNER JOIN ##sequence s ON b.b = s.val
GROUP BY b.RowOrCol, b.board
ORDER BY MAX(s.rowNumber)
```

Now that we know which board wins first, and the sequence number where it wins, we can use this query to get the number called on that sequence number and sum the rest of the numbers after it to multiply it against.  

``` sql
DECLARE @board BIGINT = 60;

select b
INTO #winningBoard
from BingoBoards
where board = @board
UNION
select i
from BingoBoards
where board = @board
UNION
select n
from BingoBoards
where board = @board
UNION
select g
from BingoBoards
where board = @board
UNION
select o
from BingoBoards
where board = @board

SELECT *
FROM #winningBoard b
INNER JOIN ##sequence s ON b.b = s.val
ORDER BY s.rowNumber
```

The sequence number you noted earlier has the value to multiply by, and the rest of the rows with greater sequence numbers are the ones to sum and multiply by the last number called.  

## Puzzle Two
Now I think I have a problem. Getting the least or the last is super easy. But for puzzle two I don't need the last called number on the last card. What I need is the first win for each card and get the last of those. I haven't got a solution there yet and it's time for bed.  