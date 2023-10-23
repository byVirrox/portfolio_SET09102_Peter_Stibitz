# Testing

This weeks Task covered Testing in general, but more specificly Unit Testing in the context
of a Hangman-MAUI-App. The task was to further complete the given Prototype of the Hangman-App 
and write test for the Methods given with their signature.

## Private Signature 

According to the task we are supposed to test the Components of GamePage the Cs-File and game
logic related to the UI during the game. 
Normally test are written for API to test whether the single components communicate in an expected manner 
between each other and therefore these methods are mostly public and accesable.
Thereby it is uncommon to test private methods.

But in this case the signatures of the methods present a problem here. Almost all of them include the
[private keyword](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/private). Therefore they can not be accessed from outside including such a coponent as our tests.
There would be a trick to overcome this obstacle: [**"Reflection"**](https://en.wikipedia.org/wiki/Reflective_programming)

Apparently after reviewing the results, reflection did not work.


## Tests 

Disclaimer:
**There had been a lot of problems with the repository. What led to most of the Tests not beeing executed until the deadline.**

It was therefore hard to judge whether a test would work or not. I will describe my thought process behind the test,
explain why ít failed, that it did apparently not fail, because the test was well written and
mention an additional test which didn't make it into the final product.

**tested piece of code**
```cs
    /* Requires testing */
    private void CreateNewChallenge()
    {
        Word = SelectWord(GameType).Trim();
        ResetDisplay(Word);
    }
```

**CreateNewChallenge_Checks_Word_Not_Null**

```cs
        /// <summary>
        /// Checks whether a Word is selcted after the CreateNewChallengeMethod has been called
        /// by asserting that its not null and an instance of string
        /// </summary>
        [Theory]
        [InlineData("Easy")]
        public void CreateNewChallenge_Checks_Word_Not_Null(string gameType)
        {
            // Arrange
            var gamePage = new GamePage(gameType);
            var createNewChallengeMethod = typeof(GamePage).GetMethod("CreateNewChallenge", BindingFlags.NonPublic | BindingFlags.Instance);
            Assert.NotNull(createNewChallengeMethod);

            // Act
            createNewChallengeMethod.Invoke(gamePage, null);

            // Assert
            Assert.NotNull(gamePage.Word);
            Assert.NotEmpty(gamePage.Word);
        }
```

The test is supposed to check whether the CreateNewChallenge() has been called or not during 
the execution. The idea behind it is to check whether the tested method called ...
```cs
Word = SelectWord(GameType).Trim();
```
... and therby changed the class-variable of word. The word should neither be null nor Empty.

The test therefore used reflection to call the described method what as we can see in the report caused an Exception leading 
the rest of the test to fail aswell:

```
[20/10/2023 2:17:10.143 PM]  [xUnit.net 00:00:00.50]         C:\Users\40000812\source\repos\RedOriginal\TestProject1\GamePageTest.cs(207,0): at Hangman.Tests.GamePageTests.CreateNewChallenge_Calls_ResetDisplay()
[20/10/2023 2:17:10.143 PM]  [xUnit.net 00:00:00.50]            at System.RuntimeMethodHandle.InvokeMethod(Object target, Void** arguments, Signature sig, Boolean isConstructor)
[20/10/2023 2:17:10.144 PM]  [xUnit.net 00:00:00.50]            at System.Reflection.MethodInvoker.Invoke(Object obj, IntPtr* args, BindingFlags invokeAttr)
````

So the test failed because it the reflection part did not work.
So what do we learn from that. First never programm blindfolded, because your dependencies apparently don't work.
Secondly that this would not have been a good test after all, because it more or less tests the result of another method.
It is still important to ensure the functionality of the woord beeing set.
A slightly better version given the methods are public is:

```cs
        [Theory]
        [InlineData("Easy")]
        public void CreateNewChallenge_Checks_Word_Not_Null(string gameType)
        {
            // Arrange
            var gamePage = new GamePage(gameType);

            // Act
            gamePage.CreateNewChallenge()

            // Assert
            Assert.NotNull(gamePage.Word);
            Assert.NotEmpty(gamePage.Word);
        }
```

**CheckLetterInWord_Checks_Return_Boolean**

```cs
        /// <summary>
        /// Checks whether CheckLetterInWord returns the right boolean values
        /// for certain inputs. 
        /// </summary>
        /// <param name="gameType">represents the game Type</param>
        /// <param name="word">Represents the word that is tested</param>
        /// <param name="letter">Letter </param>
        /// <param name="expected">expected boolean outcome</param>
        [Theory]
        [InlineData("Easy","hello",'o',true)]
        [InlineData("Easy", "hello", 'z', false)]
        [InlineData("Easy", "Hello World", ' ', false)] // the game should not include the squarespaces
        [InlineData("Easy", "hello", 'E', true)] // it shouldn't be case sensitive
        [InlineData("Easy", "Hello", 'h', true)]

        public void CheckLetterInWord_Checks_Return_Boolean(string gameType,string word,char letter, bool expected )
        {
            // Arrange
            GamePage gamePage = new GamePage(gameType);
            MethodInfo methodInfo = typeof(GamePage).GetMethod("CheckLetterInWord", BindingFlags.NonPublic | BindingFlags.Instance);
            Assert.NotNull(methodInfo);

            // Act
            var actual = (bool)methodInfo.Invoke(gamePage, new object[] { word, letter });

            // Assert
            Assert.Equal(expected, actual);

        }
```

This test sadly did not make it into the actual tests, because I did not notice after resolving a merge conflict it was 
excluded.
I still wanted to mention it eventhough, it would have had the same issue as before due to reflection.
The idea behind the test was actually better.

It should have tested different enviroments for the method:
```cs
    private bool CheckLetterInWord(string word, char answer)
    {
        LettersTried.Add(Char.Parse(answer.ToString().ToLower()));
        return word.ToLower().Trim().Contains(answer, StringComparison.OrdinalIgnoreCase);
    }
```

I first tests two standard cases, then it tries to include sqaurespaces
which would not make sense in a well programmed Hangman-Game and then testing two cases of 
Case sensitivity once as word and once as letter, that was tried.
For every pair there had been set an expected result.
Here the same issue would have been solved trying to access the method in a public way:

```cs
     [Theory]
        [InlineData("Easy","hello",'o',true)]
        [InlineData("Easy", "hello", 'z', false)]
        [InlineData("Easy", "Hello World", ' ', false)] // the game should not include the squarespaces
        [InlineData("Easy", "hello", 'E', true)] // it shouldn't be case sensitive
        [InlineData("Easy", "Hello", 'h', true)]

        public void CheckLetterInWord_Checks_Return_Boolean(string gameType,string word,char letter, bool expected )
        {
            // Arrange
            GamePage gamePage = new GamePage(gameType);

            // Act
            gamePage.CheckLetterInWord(word, letter);

            // Assert
            Assert.Equal(expected, actual);

        }
```

## Overall Reflection upon problems

There have been issues with some peoples IDEs to start with at Tuesdays practical lesson, which had to be fixed so the start of the tests was delayed.
But a mayor problem was apparently the setup of our Testproject which was missing dependencies during the whole week.
(Context: I myself have been facing some problems regarding my laptop and its OS in the last week and additional unexpected 
problems accured on Friday and after I had fixed them at least as far that I could use my laptop again, I had to start at Sunday morning. 
At that time the depencies still were not properly set up. Therefore I tried to fix them myself, but it seemed some parts still weren't 
working properly and it took a few hours.)

There stil needs to be more done in the communication, to not overlook such mayor issues but to 
overcome them and move on right at the beginning. 

Furthermore i still would like to propose not to use private methods as an example of practicing test, 
because they are normally not tested directly.

At the end this was clearly not a success, but I learned from this good code requires good organisation and communication and 
a Unit test should test one piece of code and not depend on anything else.