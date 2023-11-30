# Code review

This weeks task was to go through multiple code samples in a [Code review challenges](https://forms.office.com/e/m5ddNd4dNX)
and identify issues using the tools learned in the lecture. 

These tools include:
 * applying [general principles](https://github.com/edinburgh-napier/SET09102/blob/main/notes/05_quality_code.md#software-engineering-principles) (for example KISS,YAGNI,DRY etc.)
 * identifying and avoiding [code smells](https://github.com/edinburgh-napier/SET09102/blob/main/notes/05_quality_code.md#code-smells) (for example Middle man, Global Data, Mutable Data etc.)
 * applying [best practice rules](https://github.com/edinburgh-napier/SET09102/blob/main/notes/05_quality_code.md#use-meaningful-names) (for example commenting conventions, suitable names etc.) 


## Code Sample

This is one of the mentioned Code Samples, which will be analysed 
in the following:


**Code Sample with issues**

```cs
public string GetPlayerStatus(Player player)
{
    // 1. This displayes the unnecessary use of variable, which supposedly is meant 
    //    to later on enhance the method by adding the payername to the returned String.
    static int name = player.name;
    

    // 2. In the remaining part of this sample you can find a common example of complicated and confusing if else statements.
    if (player.IsOnline) {
        if (player.CurrentGame != null) {
            return "Player is currently in a game";
        // 3. The comment in the following line therefore shows the non-application of comment conventions.
        } else {    // Player is waiting
            if (player.PendingInvitations.Count > 0) {
                return "Player has pending invitations";
            } else {
                return "Player is online";
            }
        }
    } else {
        return "Player is offline";
    }
}
```

1. As mentioned above this Variable doesn't have any purpose at present
   and therefore is a Example for [YAGNI](https://en.wikipedia.org/wiki/You_aren%27t_gonna_need_it) (You ain't gonna need it.).
   It is very common that some programmers especially when starting with a project
   want to implement stuff halfway, before they even need it. You rather should 
   design your system that you can extend it later on if needed but you should not
   leave dead code behind in that process.

   What is strongly related is the **code smell** of **dead code** and it should be avoided to keep code that is not used anymore, because at the end it will only increase complexity.
   Another similar code smell is **Speculative Generality**, where Developers write Code in the expectation that it will be needed later on, when in reality it sometimes never happens.

3. Here is an absolutely complicated if else statement:
   First you check whether your online after that you check if a player is in the current game, 
   then you go into an else case but again end up checking whether you have invitations from other plyers.
   This is a example of [KISS](https://en.wikipedia.org/wiki/KISS_principle) (Keep it simple, Idiot).
   Maintainability and therefore readability are important factors when it comes to writing code.
   That is why you should keep the complexity of your code (such as switch cases return statements 
   around many methods or if, else statements) at a minimum.

4. In practice there are [conventions](https://learn.microsoft.com/en-us/dotnet/csharp/fundamentals/coding-style/coding-conventions#comment-style) formulated, which can help the reader and provide concistency.
   Your comment should begin in Uppercase and end on a period. But foremost it should provide
   the reader with [additional information](https://github.com/edinburgh-napier/SET09102/blob/main/notes/05_quality_code.md#comments) which helps him. That's not really the case here, because 
   waiting is the consequence of not playing but being online. The best comments are the one's that aren't there.
   So make your code as descriptive as possible.

**Improved Code Sample**

```cs
public string GetPlayerStatus(Player player)
public string GetPlayerStatus(Player player)
{
    if (!player.IsOnline)
    {
        return "Player is offline";
    }

    if (player.CurrentGame != null)
    {
        return "Player is currently in a game";
    }

    if (player.PendingInvitations.Count > 0)
    {
        return "Player has pending invitations";
    }

    return "Player is online";
}
}
```

**YAGNI**

By applying the above mentioned principles our code gets much more readable and easier to understand.
First of the unnecessary local Variable "name" gets removed. There is currently no need to provide 
the status with the player's name and there probably won't be a need in the future. So it is best to just remove 
dead code. [You ain't gonna need it](https://en.wikipedia.org/wiki/You_aren%27t_gonna_need_it)

**Use recognised commenting conventions**

Second of all the comment did also not provide the reader with any further information about the code.
Therefore it's better to let the code speak for itself.
IF you fell the urge to write a comment Apply [commenting conventions](https://learn.microsoft.com/en-us/dotnet/csharp/fundamentals/coding-style/coding-conventions#comment-style)
But last but not least structuring the if()-statements in a humanly readable order.

**KISS**

There is no use of else-statements or any nested code. Because of this we [keep it simple](https://en.wikipedia.org/wiki/KISS_principle) and just go through a
list of if statements beginning with the one which would contradict the rest. This makes it easy to 
read and the reader can distinguish the different cases fast. 

**Personal Note:**

Overall the KISS principal is not only 
applicable for code complexity, but for most tasks in Software Engineering.
The people who will later on work with your product/system or maintain it won't have a good time,
if you overcomplicate things.
People prefer simple solutions and it's genuinely harder to design a system that is simple
than a complex one.

# Internal Quality criteria

This weeks task mostly covered the internal Quality criteria:
* Acceptable names for variables, functions, classes, namespaces, etc.
* Use of comments
* Use of whitespace
* Preferred methods where alternatives exist
* Exception handling
* etc.
And there are futhermore code conventions...

# External Quality criteria
Futhermore we did not really get the chance to see any of the 5 mentioned traits of external quality, although they are really important for the requirements of a product, to ease either the customer/stakeholders. So I will include this for the sake of giving another view onto Quality in Software Development:
 * Reliability
 * Security
 * Maintainability
 * Portability
 * Usability

When I was developing an App last year at my home university, we had to develop User Stories on our own and futhermore focus on these aspect: 
* Do we have an easy deployement process so that the App is accessable on our domain for our stakeholders/professors for most of the time? (Reliability)
* Is the Data encrypted in the database? And can we provide a log-in? (Security)
* How do we structure and document our code so that it is easier to maintain? Whats our folder layout for front and backend? How to stay consistent as a team of 5 individuals? (MAintainability)
* Can we use Containerisation to roll out our system on another system? (Portability)
* Is our system accessable for everyone? Is the design of our components intuitive? (Usability)

# Reflection 
I mostly was unsure of what to expect and how to exactly identify Code smells or the non-compliance with Code Principles in review task, because you had such a big variety of options and if you are asked to critiques somethig you are going to find something, because although part of these are clearly mistakes some part of your subjective view on code will allways play a role. Futhermore most of the given code samples were too simple to really represent the code.

Overall I really tried to include a vast number of Principles here.
