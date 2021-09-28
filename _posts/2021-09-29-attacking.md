---
layout: post
title:  "Attacking"
date:   2021-09-29 13:16:28 +0000
---

# Code!
So, first things first, you've got some projects and a solution and at least one test from this [tutorial](mstest-unit), right? I do, and that's what I'm starting with. It's a nonsense test, but it's mine. I run my tests first as I'm getting started to make sure I remember where I was and that I (or someone else) didn't break things while I was away.

``` csharp
        [TestMethod]
        public void DisplayNameIncludes()
        {
            var physics = new Program();
            var name = physics.GetGameDisplayName();
            Assert.AreEqual("Utopia: The Tutorial", name);
        }
```

[mstest-unit](https://docs.microsoft.com/en-us/dotnet/core/testing/unit-testing-with-mstest)

## Test Passed
My test passed! Here's the code that allowed it to pass back when I wrote the test.

``` csharp
        public string GameTitle = "Utopia";

        public string GetGameDisplayName()
        {
            return $"{GameTitle}: The Tutorial";
        }
```

As I said before, it's a nonsense test, and the code is nonsense as well, but what I really cared about at the time was whether I'd actually remembered (or figured out) the ```dotnet``` commands correctly to get the solution, project, test project, reference from the test project to the main project, etc. all working. Now, when I run ```dotnet test`` in the main solution folder I get output confirming that the solution builds, the tests run, and the test passes. It wasn't a bad spot for stopping when I got to the end of the block of time that I had available to spend.

## Entity Component System
Might as well start setting up the architecture. I'm going to start by adding Entity as a class in the root. In its pure sense Entity is just an Id to which we're going to add Components ov various types to make things out of. So, let's write a test that reflects that.  

There's some debate about test first development, kind of a chicken or the egg type thing. Writing a test requires the class under test to exist or you get compile errors. In a way, this is definitely a failing test, but in anther way, it's not a test at all since it won't compile and run. You could go to idealistic [extremes](bradoncode) (sorry Bradon, not really calling names you post was just the first result) if you feel it's necessary, but I'm an [engineer, not a theorist](math-jokes), and I'm using tests to save time not to feel better.   

[bradoncode](http://www.bradoncode.com/blog/2012/01/asserting-exceptions-in-mstest-with.html)
[math-jokes](https://users.cs.northwestern.edu/~riesbeck/mathphyseng.html)

So, I don't really worry much about what goes first. I quick rough in a class and an attribute and write a test that "should" fail. The reason I say "should" is that with more complex code or tests, sometimes it doesn't fail when expected. Starting with a known failing state means that it is possible for the test to fail, which is important, and then fixing either the test or the code gets you your passing test. Like the chicken or the egg part above, I don't get hung up on Arrange, Act, Assert, too badly though for time savings I do generally follow the formula since then I know what to expect in my tests.    

``` csharp
        [TestMethod]
        public void DefaultEntityHasIdZero()
        {
            var entity = new Entity();
            Assert.AreEqual(entity.Id, 10);
        }
```

