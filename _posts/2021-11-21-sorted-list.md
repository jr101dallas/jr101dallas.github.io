---
layout: post
title:  "Sorted List Tests"
date:   2021-11-21 09:11:34 +0000
category: roguelike
tags: code sortedlist extension
---

# Past Due
The SortedList extensions are pretty old by now, and I never wrote any tests about them. I probably should have long since. Also, I discovered that the extensions are better than I was thinking they were!  

## Continuous Versus Sparse
I'd been thinking the whole time that I needed to be careful about keeping the keys continuous. Not so. I went to write the four test below and didn't get the failures I was expecting. After reviewing the code, I understood why they worked when I thought they wouldn't. And, that's another victory for unit tests!  

``` csharp
        [TestMethod]
        public void MaxWorksForContinuousKeys()
        {
            var sList = new SortedList<int,Item>();
            sList.Add(0, new Item());
            sList.Add(1, new Item());
            sList.Add(2, new Item());
            Assert.AreEqual(2, sList.Max().Key);
        }

        [TestMethod]
        public void MaxWorksForSparseKeys()
        {
            var sList = new SortedList<int,Item>();
            sList.Add(3, new Item());
            sList.Add(6, new Item());
            sList.Add(9, new Item());
            Assert.AreEqual(9, sList.Max().Key);
        }

        [TestMethod]
        public void MinWorksForContinuousKeys()
        {
            var sList = new SortedList<int,Item>();
            sList.Add(0, new Item());
            sList.Add(1, new Item());
            sList.Add(2, new Item());
            Assert.AreEqual(0, sList.Min().Key);
        }

        [TestMethod]
        public void MinWorksForSparseKeys()
        {
            var sList = new SortedList<int,Item>();
            sList.Add(3, new Item());
            sList.Add(6, new Item());
            sList.Add(9, new Item());
            Assert.AreEqual(3, sList.Min().Key);
        }
```

## Examine and Understand
Especially when I learn, I appreciate the closer examination of my code that detailed unit tests drive. Thinking in more detail, and writing test to assert and enforce (and remind) of the design decisions I've made, whether conscious or unconscious, helps me to keep down bugs and errors. When I'm tight on time, which is always, that's invaluable.  