# Project work 2

## Introduction

This weeks exercise continued working on the given collection of [issues](https://github.com/edinburgh-napier/SET09102/blob/main/practicals/issues/week_8.md).

Our team decided to remigrate from the master branch back to our solution, because a 
few had problems with the direct use of SQL-Syntax. 
So instead, we went back to our previous approach of a [ORM](https://en.wikipedia.org/wiki/Object–relational_mapping)-based SQLite Database.
Instead of approaching a new User Story I decided to stick to last week’s User Story, but reimplementing it for this new context.

My goal for this week was as I mentioned last week to improve my Code quality by dividing my Methods into smaller pieces and really embracing the Single Responsibility Principle.

## User Story

The User Story I repeatedly did was ([Issue#84](https://github.com/Software-Engineering-Red/MAUI-APP/issues/84)). It was about the Team Support and Logistics Manager requesting additional specialist personnel.
The Acceptance criteria consisted of:
1. Creating Requests for specialist with start Date, end Date and number of personnel required
2. Creating a Page to approve the new created Requests


## Code

So, as I mentioned earlier, I reused and adjusted apart of my previous code. The Service and Interface however were changed completely. 
More interestingly I used somewhat of the [Interface Segregation Principle](https://en.wikipedia.org/wiki/Interface_segregation_principle#:~:text=In%20the%20field%20of%20software,are%20of%20interest%20to%20them.).
A fellow teammate already provided a blueprint for implementing new tables: He provided an IService Interface which already had a Generic Crud-Interfaces and the AService which implemented them in an abstract class.
Because my application mostly was based on SkillRequest to pull my previous Interface apart. First, I casually let SkillRequestService and ISkillRequestService inherit from these Services and then 
made let ISpecialistRequestService and SpecialistRequestService inherit their functionality and thereby splitting up the interface into two pieces, one that was exclusive to RequestService that allowed creation of Unapproved Requests and a Task to Approve them.


**As an example both interfaces. One inheriting from the other**
```cs
using UndacApp.Models;

namespace UndacApp.Services
{
	public interface ISkillRequestService : IService<SkillRequest>
	{
	}
}

namespace UndacApp.Services
{
	public interface ISpecialistRequestService : ISkillRequestService
	{
		public Task<int> AddUnapprovedSkillRequest(int skillId, int numberRequired,
			DateTime startDate, DateTime endDate);
		public Task<int> approveSkillRequest(int id, Organisation organisation);
	}
}
```
One of my goals was to embrace the single responsibility Principle and breaking up methods into smaller pieces each only fulfilling one purpose.
One of the best examples for that this week was my implementation of the **OnAddRecordClicked()**-Method.
Previously all the code including try catch cases and if cases were in one single method.

I split them up into three methods making the code much easier to read and 
making each method serve only one purpose. Two getter Methods to check the UI Text Fields/ Picker and then getting the value if possible and one final Method which itself calls. 
another method to check whether the SkillRequest can be added or if the fields were empty and then finally adding a new record to the database.

```cs
...
	private void OnAddRecordClicked(object sender, EventArgs e)
	{
		var numberRequired = GetNumberRequired();
		var skillId = GetSkillId();
		var startDate = StartDatePicker.Date;
		var endDate = EndDatePicker.Date;
		AddNewSkillRequest(skillId, numberRequired, startDate, endDate);
	}

	///One example out of the three
	private void AddNewSkillRequest(int skillId, int numberRequired, DateTime startDate, DateTime endDate)
	{
		if (CheckAddable(skillId, numberRequired))
		{
			try
			{
				specialistRequestService.AddUnapprovedSkillRequest(skillId, numberRequired,
					startDate, endDate);
				DisplayAlert("Success", $"Successfully inserted record into skills.", "OK");
			}
			catch (Exception ex)
			{
				DisplayAlert("Error", $"Failed to insert record into skills. Error: {ex.Message}", "OK");
				return;
			}
		}
	}
		
...
```


## Test Code 

As I said last time, I also tried to improve writing tests. But I also noticed that most of my methods were not accessible, which was the smallest problem.
But many of them were either Part of a Crud Interface where I struggle with the Package dependencies, or they included DisplayAllerts. Therefore, I could only really test the SkillRequest and their Constructor as well as getters and setters, which is clearly not a good Unit test.
However there is a lesson to this which I will elaborte in the Reflection.

| ![week9_Tests.PNG](https://github.com/byVirrox/portfolio_SET09102_Peter_Stibitz/blob/main/images/week9_Tests.PNG?raw=true) |
|:--:|
| <b>Fig.1 - Overview of Tests</b>|

# Issues with my Code

This time I had my code reviewed by my buddy and another Teammate. My buddy found an error (of which I was already aware. However, there was a slight chance that this issue could have been caused by my Hardware.
/Development Environment, because I had a similar Exception that only occurred on my laptop in the past.). It turned out I had not correctly implemented the Interface:
```cs
	public interface ISpecialistRequestService : ISkillRequestService
	{
		public void AddUnapprovedSkillRequest(int skillId, int numberRequired,
			DateTime startDate, DateTime endDate);
		public void approveSkillRequest(int id, Organisation organisation);
	}
}

```
Instead of using a Task to asynchronously make a database request, I directly performed a method which was not performed on the mainThread leading to a:
```System.Runtime.InteropServices.COMException```
I simply resolved the issue by transforming the methods into tasks and always awaiting the requests response.
```cs
		public Task<int> AddUnapprovedSkillRequest(int skillId, int numberRequired,
			DateTime startDate, DateTime endDate);
		public Task<int> approveSkillRequest(int id, Organisation organisation);
}

```
The actuall [review](https://github.com/Software-Engineering-Red/MAUI-APP/pull/99) was later done by another teammate.

He only noticed two things:
1. That the **RequestedBy** field in **Models/SkillRequest.c**s was an integer instead of a string. But this was in fact no issue, because it this attribute is supposed to be a Foreign Key and references (presumably) the table **Person**.
2. There was a unnecessary casting in one of my components, which I removed. It Casted to an already inherited Service:
```cs
SkillRequest skillRequest = await ((SkillRequestService)this).GetOne(id);
```


# Review of my Buddy

My partner did not fully finish his current US due to him fixing a issue a Databinding Error throughout our project. So the only thing I [reviewed](https://github.com/Software-Engineering-Red/MAUI-APP/pull/103) 
were just a fix upon all Database-ContentPages that were created at the beginning of the project. My only annotation was to remove the unnecessary comments inside the Database Class.
Furthermore he found out that the BindingContext has to be set to the specific Class which one would like to Bind to the UI.


## Reflection

Although It sounds easy to just rework the User Story, considering that at least the UI was already there, it was not, which thereby shows me that my code still lacks modularity.
One principle I would like to test in the next week because of that, is implementing the architectural pattern of MVVM for my next Component making the View exchangeable and making
it easier to completely separate the View and the Logic. This would also help testing because through the current approach of mixing the ContentPages with ViewModels it is hard to really test these 
Components of my application. Most of the Methods in the .cs Files of a ContentPage were marked as private, because Part of clean and stable Code is revealing as little access as possible.

Overall, I noticed that to test methods, they have to be designed to be tested and clearly separated from the View. 
My code either was part of a Crud interface or included some kind of DisplayAlert, which are extremely useful to Debug or to explain things to the User, but they also prevent real testing.
That is why for next week I will of course once again try to write better tests, but more importantly separate my ViewModel from my View entirely (Or as far as that is possible.

I also will talk to my buddy to ensure that I can improve by reviewing code of other people.

Apart from that I had the feeling I have really improved in breaking up Components and methods into smaller bits, which was my main goal this week after all.
And I also think that converting the SQL-Syntax Version to a new approach helped me internalize some key concepts of C# like inheritance once more.



