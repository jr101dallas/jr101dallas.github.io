---
layout: post
title:  "Bad Wiring"
date:   2021-12-10 09:11:34 +0000
category: detour
tags: code sql fun
---

# Puzzling
This puzzle has to do with randomized code breaking. At least that's my interpretation of it. The basic idea is that the wires have gotten scrambled, and the output isn't displaying what ought to be displaying. You get what the display is, and you get a series of statements on signals you get on the wires, and you need to decipher what the display should say.  

## Truth Tables
I thought that my best move was to define the rules literally into some tables so I can do some joins and get my answers. But, I actually ended up with a pile of variables and logic to decipher. Here's my data insert.  

``` sql 
SELECT ROW_NUMBER() OVER(ORDER BY (SELECT NULL)) AS rowNum,
	w1, LEN(w1) AS w1l,
	w2, LEN(w2) AS w2l,
	w3, LEN(w3) AS w3l,
	w4, LEN(w4) AS w4l,
	w5, LEN(w5) AS w5l,
	w6, LEN(w6) AS w6l,
	w7, LEN(w7) AS w7l,
	w8, LEN(w8) AS w8l,
	w9, LEN(w9) AS w9l,
	w10, LEN(w10) AS w10l,
	a, LEN(a) AS al,
	b, LEN(b) AS bl,
	c, LEN(c) AS cl,
	d, LEN(d) AS dl
INTO WiresAndDisplays
FROM (
VALUES
('fcdeba','edcbag','decab','adcefg','acdfb','gdcfb','acf','fabe','fa','eacfgbd','aefb','cfa','acf','cdabf'),
('adbec','fabeg','fgda','gafedb','fadeb','cdebgf','cfaebdg','fd','bdf','cgfbae','ebdfga','fbd','bdagcef','dfb'),
...
('eg','dgaebc','dfcgab','cdgfe','edfcb','adcgf','cedagf','geaf','bacdefg','gde','dfcbe','eagf','agdecf','ge')
) t(w1,w2,w3,w4,w5,w6,w7,w8,w9,w10,a,b,c,d)
```

I knew from reading the first puzzle that I was going to need the lengths of the combinations, so the insert is a bit more complicated in order to get all the lengths calculated from the beginning. It's a little silly on the 28 columns, but when I do my processing I'll eventually take it one row (set) at a time.  

The good thing is, that by going the lengths as part of the insert, puzzle one is complete with a single query.  

``` sql 
WITH segmentCounts AS (
SELECT *
FROM (
	VALUES(0,6),
		(1, 2),
		(2, 5),
		(3, 5),
		(4, 4),
		(5, 5),
		(6, 6),
		(7, 3),
		(8, 7),
		(9, 5)
) t(val, segCount)
WHERE t.val IN(1, 4, 7, 8)
)
SELECT COUNT(s1.segCount) AS aCnt, COUNT(s2.segCount) AS bCnt, COUNT(s3.segCount) AS cCnt, COUNT(s4.segCount) AS dCnt
FROM wiresanddisplays w
LEFT JOIN segmentCounts s1 ON w.al = s1.segCount
LEFT JOIN segmentCounts s2 ON w.bl = s2.segCount
LEFT JOIN segmentCounts s3 ON w.cl = s3.segCount
LEFT JOIN segmentCounts s4 ON w.dl = s4.segCount
```

True, I didn't do the acrobatics to go one more step and add the aCnt, bCnt, etc. values but if I really need to do that for puzzle two then I will. For now I just copied the result set, put select in front and plus signs between and run it to get my answer.  

## Puzzle Two
Ok, now is the logic heavy part. I usually build my loop logic as a single iteration first and then build in the loop, so this is how far I got when I saw I had a problem. First I decided that I'd rather deal with the data sets pivoted.  

``` sql
CREATE TABLE WireAndDisplayPivot (rowNum INT, combo VARCHAR(10), comboLen INT)

WITH cte AS (
	SELECT rowNum,
		w1,w1l,w2,w2l,w3,w3l,w4,w4l,w5,w5l,w6,w6l,w7,w7l,w8,w8l,w9,w9l,w10,w10l,a,al,b,bl,c,cl,d,dl
	FROM wiresanddisplays
	--WHERE rowNum = 1
)
INSERT INTO WireAndDisplayPivot (rowNum , combo , comboLen )
SELECT rowNum, w1 AS combo, w1l AS comboLen FROM cte
UNION 
SELECT rowNum, w2 AS combo, w2l AS comboLen FROM cte
UNION
SELECT rowNum, w3 AS combo, w3l AS comboLen FROM cte
UNION
SELECT rowNum, w4 AS combo, w4l AS comboLen FROM cte
UNION
SELECT rowNum, w5 AS combo, w5l AS comboLen FROM cte
UNION
SELECT rowNum, w6 AS combo, w6l AS comboLen FROM cte
UNION
SELECT rowNum, w7 AS combo, w7l AS comboLen FROM cte
UNION
SELECT rowNum, w8 AS combo, w8l AS comboLen FROM cte
UNION
SELECT rowNum, w9 AS combo, w9l AS comboLen FROM cte
UNION
SELECT rowNum, w10 AS combo, w10l AS comboLen FROM cte;
```

Then I put together this logic for the deciphering.  

``` sql
DECLARE @TestSet TABLE(rowNum INT, combo VARCHAR(10), comboLen INT)
DECLARE @DigitsToCombos TABLE(digit INT, combo VARCHAR(10))
DECLARE @OneCombo VARCHAR(10),
	@TwoCombo VARCHAR(10),
	@ThreeCombo VARCHAR(10),
	@FourCombo VARCHAR(10),
	@FiveCombo VARCHAR(10),
	@SixCombo VARCHAR(10),
	@SevenCombo VARCHAR(10),
	@EightCombo VARCHAR(10),
	@NineCombo VARCHAR(10),
	@ZeroCombo VARCHAR(10),
	@RightWireOne CHAR(1),
	@RightWireTwo CHAR(1),
	@MiddleAndUpperLeft CHAR(2),
	@MiddleOrUpperLeftOne CHAR(1),
	@MiddleOrUpperLeftTwo CHAR(1),
	@Middle CHAR(1),
	@UpperLeft CHAR(1)

INSERT INTO @TestSet
SELECT * 
FROM WireAndDisplayPivot
WHERE rowNum = 1

SELECT @OneCombo = combo FROM @TestSet WHERE comboLen = 2
SELECT @FourCombo = combo FROM @TestSet WHERE comboLen = 4
SELECT @SevenCombo = combo FROM @TestSet WHERE comboLen = 3
SELECT @EightCombo = combo FROM @TestSet WHERE comboLen = 7

SELECT @RightWireOne = LEFT(combo,1), @RightWireTwo = RIGHT(combo,1) FROM @TestSet WHERE comboLen = 2

SELECT @MiddleAndUpperLeft = REPLACE(
			REPLACE(
				@FourCombo
			, @RightWireOne, '')
		, @RightWireTwo, '')

SELECT @MiddleOrUpperLeftOne = LEFT(@MiddleAndUpperLeft, 1), @MiddleOrUpperLeftTwo = RIGHT(@MiddleAndUpperLeft, 1)

SELECT @NineCombo = combo FROM @TestSet WHERE comboLen = 6 
	AND combo like '%' + @RightWireOne + '%' 
	AND combo like '%' + @RightWireTwo + '%'
	AND combo like '%' + @MiddleOrUpperLeftOne + '%'
	AND combo like '%' + @MiddleOrUpperLeftTwo + '%'

SELECT @SixCombo = combo FROM @TestSet WHERE comboLen = 6 
	AND combo like '%' + @MiddleOrUpperLeftOne + '%'
	AND combo like '%' + @MiddleOrUpperLeftTwo + '%'
	AND combo != @NineCombo

SELECT @ZeroCombo = combo FROM @TestSet WHERE comboLen = 6
	AND combo != @NineCombo
	AND combo != @SixCombo

SELECT @Middle = CASE WHEN @ZeroCombo LIKE '%' + @MiddleOrUpperLeftOne + '%' THEN @MiddleOrUpperLeftTwo ELSE @MiddleOrUpperLeftOne END,
	@UpperLeft = CASE WHEN @ZeroCombo LIKE '%' + @MiddleOrUpperLeftOne + '%' THEN @MiddleOrUpperLeftOne ELSE @MiddleOrUpperLeftTwo END

SELECT @FiveCombo = combo FROM @TestSet WHERE comboLen = 5
	AND combo LIKE '%' + @UpperLeft + '%'

SELECT @ThreeCombo = combo FROM @TestSet WHERE comboLen = 5
	AND combo LIKE '%' + @RightWireOne + '%'
	AND combo LIKE '%' + @RightWireTwo + '%'

SELECT @TwoCombo = combo FROM @TestSet WHERE comboLen = 5
	AND combo != @FiveCombo
	AND combo != @ThreeCombo

INSERT INTO @DigitsToCombos
SELECT *
FROM (
	VALUES(0, @ZeroCombo),
		(1, @OneCombo),
		(2, @TwoCombo),
		(3, @ThreeCombo),
		(4, @FourCombo),
		(5, @FiveCombo),
		(6, @SixCombo),
		(7, @SevenCombo),
		(8, @EightCombo),
		(9, @NineCombo)
) t(digit, combo)

SELECT w.*, a.digit, b.digit, c.digit, d.digit
FROM WiresAndDisplays w
LEFT JOIN @DigitsToCombos a ON w.a = a.combo
LEFT JOIN @DigitsToCombos b ON w.b = b.combo
LEFT JOIN @DigitsToCombos c ON w.c = c.combo
LEFT JOIN @DigitsToCombos d ON w.d = d.combo
WHERE w.rowNum = 1
```

But, it doesn't work. The letter combinations aren't consistent within the first row. So I'm going to have to do some data scrubbing.  

``` sql
CREATE OR ALTER FUNCTION LetterToBitConverter(@combo VARCHAR(10))
RETURNS TABLE
RETURN
WITH conversion AS (
	SELECT letter, bits
	FROM (
		VALUES('a', 1),
		('b', 2),
		('c', 4),
		('d', 8),
		('e', 16),
		('f', 32),
		('g', 64)
	) v(letter, bits)
)
SELECT @combo AS combo, SUM(bits) AS BitSequence
FROM (
	SELECT letter, bits FROM conversion WHERE @combo LIKE '%' + letter + '%' AND letter = 'a'
	UNION
	SELECT letter, bits FROM conversion WHERE @combo LIKE '%' + letter + '%' AND letter = 'b'
	UNION
	SELECT letter, bits FROM conversion WHERE @combo LIKE '%' + letter + '%' AND letter = 'c'
	UNION
	SELECT letter, bits FROM conversion WHERE @combo LIKE '%' + letter + '%' AND letter = 'd'
	UNION
	SELECT letter, bits FROM conversion WHERE @combo LIKE '%' + letter + '%' AND letter = 'e'
	UNION
	SELECT letter, bits FROM conversion WHERE @combo LIKE '%' + letter + '%' AND letter = 'f'
	UNION
	SELECT letter, bits FROM conversion WHERE @combo LIKE '%' + letter + '%' AND letter = 'g'
) t(letter, bits)
GO

SELECT w.*, ca_a.BitSequence AS BitsA, ca_b.BitSequence AS BitsB, ca_c.BitSequence AS BitsC, ca_d.BitSequence AS BitsD
INTO DisplaysWithBits
FROM WiresAndDisplays w
CROSS APPLY dbo.LetterToBitConverter(w.a) ca_a
CROSS APPLY dbo.LetterToBitConverter(w.b) ca_b
CROSS APPLY dbo.LetterToBitConverter(w.c) ca_c
CROSS APPLY dbo.LetterToBitConverter(w.d) ca_d
```

That's going to get me individual digits so there's a bit more conversion to do to get the integers so I can just sum them.  

``` sql
CREATE OR ALTER FUNCTION DigitsToIntConverter(@First INT, @Second INT, @Third INT, @Fourth INT)
RETURNS TABLE
RETURN
SELECT CONVERT(INT, 
	CONVERT(VARCHAR(10), @First) + CONVERT(VARCHAR(10), @Second) + CONVERT(VARCHAR(10), @Third) + CONVERT(VARCHAR(10), @Fourth)
	) AS DisplayNumber
GO
```

That should do it. Here's my updated logic with the joind on the bit sequences for the combos and the looping logic.  

``` sql
CREATE TABLE DigitsToSum (rowNum INT, displayNum INT)

DECLARE @TestSet TABLE(rowNum INT, combo VARCHAR(10), comboLen INT)
DECLARE @DigitsToCombos TABLE(digit INT, combo VARCHAR(10), bits INT)
DECLARE @Iteration INT = 1, @RowCount INT = (SELECT COUNT(0) FROM DisplaysWithBits),
	@OneCombo VARCHAR(10),
	@TwoCombo VARCHAR(10),
	@ThreeCombo VARCHAR(10),
	@FourCombo VARCHAR(10),
	@FiveCombo VARCHAR(10),
	@SixCombo VARCHAR(10),
	@SevenCombo VARCHAR(10),
	@EightCombo VARCHAR(10),
	@NineCombo VARCHAR(10),
	@ZeroCombo VARCHAR(10),
	@RightWireOne CHAR(1),
	@RightWireTwo CHAR(1),
	@MiddleAndUpperLeft CHAR(2),
	@MiddleOrUpperLeftOne CHAR(1),
	@MiddleOrUpperLeftTwo CHAR(1),
	@Middle CHAR(1),
	@UpperLeft CHAR(1)

INSERT INTO @TestSet
SELECT * 
FROM WireAndDisplayPivot3
WHERE rowNum = @Iteration

WHILE @Iteration <= @RowCount 
BEGIN
	SELECT @OneCombo = combo FROM @TestSet WHERE comboLen = 2
	SELECT @FourCombo = combo FROM @TestSet WHERE comboLen = 4
	SELECT @SevenCombo = combo FROM @TestSet WHERE comboLen = 3
	SELECT @EightCombo = combo FROM @TestSet WHERE comboLen = 7

	SELECT @RightWireOne = LEFT(combo,1), @RightWireTwo = RIGHT(combo,1) FROM @TestSet WHERE comboLen = 2

	SELECT @MiddleAndUpperLeft = REPLACE(
				REPLACE(
					@FourCombo
				, @RightWireOne, '')
			, @RightWireTwo, '')

	SELECT @MiddleOrUpperLeftOne = LEFT(@MiddleAndUpperLeft, 1), @MiddleOrUpperLeftTwo = RIGHT(@MiddleAndUpperLeft, 1)

	SELECT @NineCombo = combo FROM @TestSet WHERE comboLen = 6 
		AND combo like '%' + @RightWireOne + '%' 
		AND combo like '%' + @RightWireTwo + '%'
		AND combo like '%' + @MiddleOrUpperLeftOne + '%'
		AND combo like '%' + @MiddleOrUpperLeftTwo + '%'

	SELECT @SixCombo = combo FROM @TestSet WHERE comboLen = 6 
		AND combo like '%' + @MiddleOrUpperLeftOne + '%'
		AND combo like '%' + @MiddleOrUpperLeftTwo + '%'
		AND combo != @NineCombo

	SELECT @ZeroCombo = combo FROM @TestSet WHERE comboLen = 6
		AND combo != @NineCombo
		AND combo != @SixCombo

	SELECT @Middle = CASE WHEN @ZeroCombo LIKE '%' + @MiddleOrUpperLeftOne + '%' THEN @MiddleOrUpperLeftTwo ELSE @MiddleOrUpperLeftOne END,
		@UpperLeft = CASE WHEN @ZeroCombo LIKE '%' + @MiddleOrUpperLeftOne + '%' THEN @MiddleOrUpperLeftOne ELSE @MiddleOrUpperLeftTwo END

	SELECT @FiveCombo = combo FROM @TestSet WHERE comboLen = 5
		AND combo LIKE '%' + @UpperLeft + '%'

	SELECT @ThreeCombo = combo FROM @TestSet WHERE comboLen = 5
		AND combo LIKE '%' + @RightWireOne + '%'
		AND combo LIKE '%' + @RightWireTwo + '%'

	SELECT @TwoCombo = combo FROM @TestSet WHERE comboLen = 5
		AND combo != @FiveCombo
		AND combo != @ThreeCombo

	INSERT INTO @DigitsToCombos
	SELECT t.digit, t.combo, ca.BitSequence
	FROM (
		VALUES(0, @ZeroCombo),
			(1, @OneCombo),
			(2, @TwoCombo),
			(3, @ThreeCombo),
			(4, @FourCombo),
			(5, @FiveCombo),
			(6, @SixCombo),
			(7, @SevenCombo),
			(8, @EightCombo),
			(9, @NineCombo)
	) t(digit, combo)
	CROSS APPLY dbo.LetterToBitConverter(t.combo) ca

	INSERT INTO DigitsToSum (rowNum, displayNum)
	SELECT w.rowNum, ca.DisplayNumber
	FROM DisplaysWithBits w
	LEFT JOIN @DigitsToCombos a ON w.BitsA = a.bits
	LEFT JOIN @DigitsToCombos b ON w.BitsB = b.bits
	LEFT JOIN @DigitsToCombos c ON w.BitsC = c.bits
	LEFT JOIN @DigitsToCombos d ON w.BitsD = d.bits
	CROSS APPLY dbo.DigitsToIntConverter(a.digit, b.digit, c.digit, d.digit) ca
	WHERE w.rowNum = @iteration

	DELETE @TestSet
	DELETE @DigitsToCombos

	SELECT @iteration = @iteration + 1

	INSERT INTO @TestSet
	SELECT * 
	FROM WireAndDisplayPivot3
	WHERE rowNum = @Iteration
END

SELECT SUM(displayNum) AS TheAnswer
FROM DigitsToSum
```

This wasn't one of the cleanest puzzle solutions. I'm thinking I could spend some time cleaning things up and making them better but I'm more excited to move on to the other days. Maybe some other time.  
