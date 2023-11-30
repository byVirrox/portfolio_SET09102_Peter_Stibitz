# Project work 5

## Introduction

This is the last and final Week therefore I will try to cover as much as I can.
I will go through a bit of an alternative problem solution to last week’s problem of creating a query and discuss downsides of ORM to casual SQL-Databases.
After that I will include some MVVM dependency injection, and I will cover a bit of Testing.

And in my Reflection, I will try to analyse our overall workflow and development process a bit. 

## User Story

The User Story I finally finished this week was ([Issue#83](https://github.com/Software-Engineering-Red/MAUI-APP/issues/83)). It was about assigning Resources such as food, medical Supplies etc. to Operations.
The Acceptance criteria consisted of:
1. ~~Creating Filters and clearing them~~
2. ~~Sort the Operations and highlight those with new Requests~~
3. ~~Approving the Requests~~
4. ~~Showing details of the operations~~
5. ~~Showing all of the Request for a Operation~~

I now finished a Page to display Operations which can be filtered now. I had to rework some aspects, because a Teammate did change something on one specific Model and its corresponding ContentPage which took a lot of time to restore.

## Query Problem

As before way of trying to create a query with the SQLite.net Extension did not result in a success.

The main issue here is that the Extension is not official, has still many open Issues and overall, a poor Documentation.

Therefore, the only alternative was using already implemented Services and combining them by using Lambda Expressions to create a sort-of-query.
```cs
		Task<List<int>> GetOperationIdsWithPendingRequests();
		Task<List<OperationResourceRequest>> GetPendingRequestsByOperation(Operation operation);
		Task<int> ApproveRequest(OperationResourceRequest request);
```

I wrote a Task to get the Ids of all Operations with Pending Request, one to get all Pending Requests of a specific Operation and a last one to approve Request.
Exemplary I will demonstrate my proceed on one private Method that is called by the ones above:
 ```cs
		private async Task<List<OperationResourceRequest>> GetPendingRequestsByTeams(List<OperationalTeam> teams)
		{
			var allRequests = await operationRequestService.GetAll();
			if (allRequests == null)
				return new List<OperationResourceRequest>();

			List<int> operationalTeamIds = teams.Select(team => team.ID).ToList();
			if (operationalTeamIds == null)
				return new List<OperationResourceRequest>();

			var teamsWithOperationID = allRequests.Where(request => operationalTeamIds.Contains(request.OperationalTeamId) && 
			request.Status == OperationResourceRequestStatus.Pending).ToList();
			if (teamsWithOperationID != null)
				return teamsWithOperationID;
			return new List<OperationResourceRequest>();
		}
```

I mostly used the Services to first get all Objects of a Table. Then I accessed another table with my Service and Selected the Objects that were somehow connected.
In this case through getting first all Ids of Team Objects and then using a Where clause to get only the requests that contained the same OperationalTeamId as Contained in the acquired List.

Overall this is by far not as elegant as a SQL query, but for the most part should here symbolise the downsides of using ORMs.
* On One hand they are really handy especially for people with no experience with Databases, because records get treated like objects.
* But the downsides are that the tables are separate and its harder to implement Foreign relationships afterwards. Overall, a normal database seems to be much more stable construct and certainly would have been beneficial and more appropriate for an App that has such complex relations in its Database-Structure and requires a certain stability.
In a previous version we used SQL syntax and SQLite directly, but as a result, some had problems of handling a normal SQL-Database I decided to follow along and not raise an objection.


## Dependency Injection 

Although I am familiar with this design-Pattern, I did not apply it throughout the whole project.
But in this case, I wanted to share an example how to so, what makes it so easy and why its so brilliant.

```cs
		builder.Services.AddSingleton<IOperationService, OperationService>();
		builder.Services.AddSingleton<FindPendingOperationResourceRequestViewModel>();
        builder.Services.AddSingleton<FindOperationResourceRequestPage>();
```
Basically we just initiate each neccessary component for our builup including our Views, ViewModels or Services as a Singleton at the build-up of the Project.
```cs
	public FindOperationResourceRequestPage(FindPendingOperationResourceRequestViewModel viewModel)
	{
		this.viewModel = viewModel;
		InitializeComponent();
		BindingContext = viewModel;
	}
```
Allowing us simply to add those Singletons when initiating some sort of Page and at the same time preventing us from always creating new Objects. 
The nice thing is that we can derive our Services and View Models always from the root of our project without worrying about why something was not initiated, because of a missing Dependency

It is a really useful Design Pattern that probably would have helped me a lot earlier on.


## Interface Segragation Principle

```cs
namespace UndacApp.Services
{
	public interface IOperationResourceRequestService : IService<OperationResourceRequest>
	{
		Task<int> ApproveRequest(OperationResourceRequest request);
	}
}
```

Here is An example I retospectifly added because it simply demostrates what the principle does. In basic Terms we want to seperate our interfaces and only include the methods we really need. In my case I allready inherited from the IService which represents basic CRUD-Interface functionalities and than implement an additional method which so only serves the pupose of accepting a request. It would not have made sense to implement it for each Service because not every Model represents a Request.

# Tests 

Someone fixed our building Problem by renaming the Resource Class into AResource. The main issue here was assumably that the compiler got confused because other included packages included Resource as a Class as well.

| ![week12_tests.png](https://github.com/byVirrox/portfolio_SET09102_Peter_Stibitz/blob/main/images/week12_tests.png?raw=true) |
|:--:|
| <b>Fig.1 - ERD with Relationship I try to map</b>|

I have done 3 Tests for this week to Test my Service which should substitute for a real Query.
Basically, I tested each method once. Here is some Example code to follow along.

Before we start our test, we should pass in some data. It is better to really separate the data directly from the test so that it is easier adjustable later on:

```cs
		public static IEnumerable<object[]> GetPendingRequestsTestData()
		{
		yield return new object[]
			{
			new List<Operation>
			{
				new Operation { ID = 101 },
				new Operation { ID = 102 },
			},
			new List<OperationalTeam>
			{	
				new OperationalTeam { ID = 1, OperationId = 101 },
				new OperationalTeam { ID = 2, OperationId = 102 },
			},
			new List<OperationResourceRequest>
			{
				new OperationResourceRequest { OperationalTeamId = 1, Status = OperationResourceRequestStatus.Pending },
				new OperationResourceRequest { OperationalTeamId = 2, Status = OperationResourceRequestStatus.Pending },
			},
			new List<int> { 101, 102 },
			};
		}
```
By using IEnumerable we can create Data-Objects to later pass them into our Methods by using [MemberData(nameof(GetPendingRequestsTestData))].
This has the advantage that the set of data is reusable for the same Method with a different test scenario or a Method with the same parameters.

```cs
		[Theory]
		[MemberData(nameof(GetPendingRequestsTestData))]
		public async Task GetOperationIdsWithPendingRequests_ReturnsCorrectIds(
			List<Operation> operations,
			List<OperationalTeam> teams,
			List<OperationResourceRequest> requests,
			List<int> expectedIds)
		{
			// Arrange
			var operationServiceMock = new Mock<IOperationService>();
			var teamServiceMock = new Mock<IOperationalTeamService>();
			var operationRequestServiceMock = new Mock<IOperationResourceRequestService>();

			var pendingResourceRequestService = new PendingResourceRequestService(
				operationServiceMock.Object, teamServiceMock.Object, operationRequestServiceMock.Object);

			operationServiceMock.Setup(mock => mock.GetOne(It.IsAny<int>()))
				.ReturnsAsync((int id) => operations.Find(o => o.ID == id));

			teamServiceMock.Setup(mock => mock.GetOne(It.IsAny<int>()))
				.ReturnsAsync((int id) => teams.Find(t => t.ID == id));

			operationRequestServiceMock.Setup(mock => mock.GetAll())
				.ReturnsAsync(requests);

			// Act
			var result = await pendingResourceRequestService.GetOperationIdsWithPendingRequests();

			// Assert
			Assert.NotNull(result);
			Assert.Equal(expectedIds, result);
		}
```

Principally Tests always follow the structure arrange, Act, Assert. 

So, we first set Up everything. In this case I made a few Mock Services that would imitate my desired behaviour.

Then we Act and execute the methods we would like to test.

And at the end we check whether that was our inteded behaviour. We assert.
In this case we first check whether a result had been returned and than if we got in our case the expected two Resources inside of a List.

In some cases we need a teardown meaning we clean the traces of our test not to influence the results of any other test.

## Review of my Code

My Buddy [reviewed my code](https://github.com/Software-Engineering-Red/MAUI-APP/pull/195) this week. He did not relly find any issues.

## Code Reviews

This week there had been a lot less workload with regard to Code Reviews:

There only had been a three behaviours I pointed out in my [buddies Pull Request](https://github.com/Software-Engineering-Red/MAUI-APP/pull/196):
```cs
	// Private fields for service instances and observable collections
    private EquipmentService Equipmentservice = new EquipmentService();
    private LogisticsService operationService = new LogisticsService();
    public AModel SelectedEquipment { get; set; }
....
```
First of some of his comments seemed pretty uneccessary, because they only explained what already was obvious. Generally comments should rather not be used and if they are used 
it should be for far more coplex matters.

```cs
else
            {

                throw new InvalidOperationException("Please select both a team member and an operation before adding.");
```

Second of all that he threw an exception if some parameter was missing in the ContentPage instead of using an Alarm to get the Users Attention.

```cs
        foreach (var operation in operations)
        {
            Operations.Add(operation);

        }
```
And last of all he could have used Lambda expressions here, due to them saving space and being easier to understand here.


## Reflection

Lastly I wanted to reflect upon the whole development process:

### Experience
First of for me I surely gained some experiences through learning different Code smells and applying Code principles or patterns like dependency Injection, ORM or MVVM.
This definitely helped me improve my personal workflow. 

### AI Support and CASE
I would also like to point out that I am mostly used a combination of an AI-guided Development process through using ChatGPT in the enitre project. Computer-aided software engineering is very helpful and should not be seen as a thread rather than a tool.

As I was an absolute beginner in C# and although it did not that much differ from 
Java it helped me a lot using ChatGPT for solving minor problems and familiarizing myself with MAUI and everything surrounding it. I mostly constructed the bigger picture on a higher level but it certainly helps using it as a guide of how to 
implement some more detailed work or for just making recommendations on how to do something. 

Most of the time it first helped asking Chat GPT to imlement directly some sort of feature get an impression of the sollution and than decide whether that fits your intended functionality and whether it is clear what the code does. If it's not rephrase your prompt. If the functionality is given, but your not happy how chatGPT implemented it you either adjust the code or try to let ChatGPT to adjust its sollution.

Either way just by using it the developer automatically will be confronted with new concepts of a language that he might have skipped if he just read the basic documentation of a framework.

But the usage of a AI always requires you to know exactly what you are doing, what terms to use and how to efficiently tell the AI what to do. 

And sometimes ou have already done something and either want to extract or to adjust something similar. You know how it would work, but it sometimes is just easier to safe your time and to generate it. It safes time, that you can use to think about more important aspects.


### Development process as a Team 
I also would like to use this as a reflection and constructive criticism. I already worked on a Web App for a whole semester last year, so I have a bit of experience how to approach a project.
First, I would like to address the setting and size of our Group and the Project.
I know it was intended to be open and that's per se not a bad thing. The problem was a bit that the size of the teams made communication so hard to the point where most of the people avoided it.
A group-size of 3-7 People works better especially with an agile approach and because as this trimester showed Software Engineering is mostly about communication on a technical level.

This led to many issues, some people did not use the given Entity-Relationship-Diagram as a resource resulting in kind of a mess. We mostly still worked on our Task on our own when it would have been more effective using the given two hours each week for coordination.
But this chaos led to lack of motivation for many people.

Last semester we did 2-3 Daily Scrum Meeting each week resulting in a much higher productivity, because we always wanted to share our progress.

Overall, our workflow mostly consisted on the basic repository management. We had a master, a develop breached of the master and feature branches branched of develop.
This especially lead to confusion at the beginning, but apart from some merging issues I feel like that was not really a big issue later on. 

What would have helped our Team a lot would have been a planning phase and a high-level planning of our App, to implement such things as a database-Manager early on and agree on a Design Pattern like MVVM, a folder layout for that.
and in general, just sharing knowledge so that everyone would have for example used Dependency Injection from the beginning.

As I said mostly I would lead most of the issues we had on the [size of the team](https://www.artofteamwork.com/what-is-the-ideal-team-size/). In smaller teams everyone gets his time to contribute and share something, but with rising size it gets more and more complicated to plan out things and find compromises and overall getting to know other team members really.

### Requirement Engineering, Planning, Desiging
What also was missging was properly thinking about requirements and than apply these requiremets though carefully designing some sort of **High-level-Design**. Although we were provided with the Entity Relationship Diagramm and therby at least had some sort of **Data structure design**, many team members did not follow allong. 
Defining **User or System Requirements** is essential for finding **Abstract specification of each sub-system**
We would not have used any **algorithm design**, but it would have been benifial to think about some sort of **Architectual Design and ComponentDesign** through using methods like:
* Block Diagramms
* UML Class Diagramms
* or as a lightweight alternative the C4 Mdoel

Really going ito detail will help a the overall development process because the tema is automatically going to appproach their tasks not as single issues but in the context of a larger scale,
Additionally allthough that is not the focus it would have been better to do some **Interface Design** or rather think about how to standardise our USer interfaces. And you do not have to use Tools like Figma necessarily. Just use pen and paper and design a quick wireframe by using a hand drawing and thinking about what the user will need to clearly uderstand the component. 

This poor planning or lack of communication also caused that noone would approach the task of some sort of Log-In service eventhough it's mostly just a standard feature almost any App has and it would not have been that difficult to implement (, if you not tottally serious about security).

### Conclusion 
Although we had our flaws with our Workflow as a Team, we could make some progress and I personally learned a lot about C#, The .NET-World and could familiarise myself with many Aspects of Development, and overall gain a wider knowledge of applicable patterns for different Tasks and far and foremost Code-Quality in practice as different Code Principles (KISS, YAGNII ...) or guidelines to avoid certain mistakes.

