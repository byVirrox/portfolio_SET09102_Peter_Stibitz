# Project work 1


## Introduction

This weeks exercise was to work on one issue we could chose out of this collection of [issues](https://github.com/edinburgh-napier/SET09102/blob/main/practicals/issues/week_8.md), write tests and documentation,
get Feedback through a review and review one other issue out of your team.

My goal for this week was to finish my issue and trying to make a stable sub-application
for my task.

## User Story

The User Story I worked on ([Issue#84](https://github.com/Software-Engineering-Red/MAUI-APP/issues/84))was about the Team Support and Logistics Manager requesting additional specialist personnel.
The Acceptance criteria consisted of:
1. Creating Requests for specialist with start Date, end Date and number of personnel required
2. Creating a Page to approve the new created Requests

## Code

For the context: Our team decided to change the branch we are currently working on because a fellow Team Member (Justin)
wrote a component uniting all tables we previously wrote separately in our own components.

Starting from there I first wanted to extend his Component, but I soon discovered that this is not as easy as it seems,
because it was designed around the idea that every table has an Id and a Name, which is not exactly the case with request skill and its 
referenced tables.

So instead, the code I have written mainly turns around one Interface implemented in a service, which uses SQL-Queries to create and update Specialist Requests.
As the reader can recognise, I tried to apply YAGNI for my specific task. Instead of implementing a CRUD interface as a whole modifying the 
new I only implemented the necessary parts.


```cs
	internal interface ISpecialistRequestService
	{
		// Adds new skill Request (synonymous with Specialist Requests)
		void AddUnapprovedSkillRequest(string skillName, int numberRequired, 
			DateTime startDate,DateTime endDate);

		// Get all skill Requests as a List 
		List<SkillRequest> GetAllSkillRequests();

		// Approve Skill Request
		void approveSkillRequest(int id, int organisationId);
	}

```
I wrote the required Database-Tables, a model, some Test-Values that are always Initialized and two Content Pages using the interface to portrait the Requests and create them.

I also tried to outsource a few things into new smaller Units and Methods instead of building huge Methods that try to 
handle every necessary process. This was mainly to follow along with the single responsibility principle. Example FROM this:

```cs
...
			using (var reader = command.ExecuteReader())
			{
				while (reader.Read())
				{
					SkillRequest skillRequest = new SkillRequest
					{
						Id = reader.GetInt32(0),
						SkillName = reader.GetString(1),
						OrganisationId = reader.GetInt32(2),
						RequestDate = reader.GetDateTime(3),
						RequestedBy = reader.GetInt32(4),
						NumberRequired = reader.GetInt32(5),
						StartDate = reader.GetDateTime(6),
						EndDate = reader.GetDateTime(7),
						Status = reader.GetString(8),
						ConfirmedDate = reader.GetDateTime(9)
					};

					skillRequests.Add(skillRequest);
				}
			}
...
```

To THIS:
```cs
...
    using (var reader = command.ExecuteReader())
    {
        while (reader.Read())
        {
            SkillRequest skillRequest = CreateSkillRequestFromReader(reader);
            skillRequests.Add(skillRequest);
        }
    }
...
```
Overall I tried to minimise methods, but due to the large SQL-Queries and to still keep things readable some methods are pretty large.
I also tried to not realy use any comments. The only instance where i used them, was to mark my Test-Values in the DatabaseInitialser.

## Test Code 

I was only able to write one appropriate test to check whether a value is added to the databases.
Therefore i drop the request skill table at beginning, create a new record and check via SQL-Query if it got added to the table.
The Team did not set up a Test Environment in our project so I just made a local xUnit Project.

# Issues with my Code

As far as my buddy could tell in his [review](https://github.com/Software-Engineering-Red/MAUI-APP/pull/99) there was one slight piece of code that throws an error but did not 
restrict the functionality of my components. 
It has something to do with the binding of one Datepicker. Unfortunately I 
did not find a way yet how to resolve that issue but still keep the functionality that the start Date 
becomes the minimum value of End Date.

Furthermore, he annotated that the merge conflicts would have to be resolved.

# Review of my Buddy

The [Review](https://github.com/Software-Engineering-Red/MAUI-APP/pull/99) of my buddies Code has revealed, that on one instance he had to hardcode something instead of getting it from the database, which is of course bad practice.
I made another comment about one very large method with several if cases which could easily be reduced by outsourcing that bit.


## Reflection

There have been a few instances, whilst coding where I noticed that I copied parts of
the given DatabasesOperations Service and just slightly modified them.
But due to its complexity and dependencies it was easier to just build a new Service.
This made the whole process rather inefficient and if I wouldn't have worked on my specific issue yet, 
it would have been more effective to prepare a Database Manager for all tables.

I tried to pay attention to only write code that I need, as you saw in the Interface example and trying
to minimalize the components to maximise the flexibility with them. 

But I still notice there is a lot of room for improvement because many methods are still around 50 lines of Code.
This might be due to the long SQL-Queries. 
Nonetheless it is bad Code Praxis and should be avoided.
Therefore, I would like to use the next two weeks to improve that behaviour and 
making following the Single Responsibility Principle.

I also tried to structure my Code as readable as possible, ordering if statements underneath not combining too many. 
with &&s and overall keeping the method internal complexity at a minimum.

From looking at the previous Code of Justin, I also discovered how useful it can be using try catch statements during coding.
This makes debugging a lot easier and provides a certain stability to the code although,
it is always more important preventing Exceptions from being thrown.

I have also noticed that I really struggle writing tests in general, but in particular that can provide significant informative value whether a component does work or not.
Last semester at my home university we had a similar but much more demanding module, where we developed an App.
We were required to do [Usability](https://github.com/edinburgh-napier/SET09102/blob/main/notes/Week06b_testing.md#usability-testing) 
and [Integration Tests](https://github.com/edinburgh-napier/SET09102/blob/main/notes/Week06b_testing.md#integration-testing), but we skipped doing
writing Unit Tests, because our grade was based on the application itself and not how well it was tested.
Luckily or sadly that totally worked out for my team, and we even were entitled the best Team of that semester.
So, besides my problems with the writing of tests, I must overcome subliminal feeling that Unit-testing is not necessary, 
even though rationally speaking I know it is important to ensure code functionality and maintainability. 
Hence that I will set my focus on testing next week and trying to communicate an adequate workflow for tests with my team.
