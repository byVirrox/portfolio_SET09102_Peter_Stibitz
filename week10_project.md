# Project work 3

# Introduction

This time I was not able to finalise my Issue I selected at the beginning of the week, but I could fulfil one of my goals from last week which was to 
familiarise myself with the MVVM Design Pattern. Working on the issue made me realise, that it was far more complex than I initially thought, and it required much more aspects build around it.

So, this week’s Portfolio will mostly focuses on MVVM. 

## User Story

The User Story I started this week was ([Issue#83](https://github.com/Software-Engineering-Red/MAUI-APP/issues/83)). It was about assigning Resources such as food, medical Supplies etc. to Operations.
The Acceptance criteria consisted of:
1. Creating Filters and clearing them 
2. Sort everything according to Operations and highlighting those with new Requests
3. Approving the Requests 
4. Showing details of the Operations 

I mainly focused on the aspect of Requesting Resources. Overall, three new tables had to be added, which is why the Issue could not be completed fully.

## MVVM

As I said in the introduction, I would like to focus on the aspect of MVVM Design-Pattern. 

MVVM stands for [Model View ViewModel](https://en.wikipedia.org/wiki/Model_View_ViewModel)..

It was largely Promoted through Microsoft and their Products like .NET MAUI or Blazor, but mostly is a Design Pattern used for WebDevelopment.

The idea is pretty brilliant: Instead of connecting our View closely to our business logic making it an absolute mess if you would like to update. 
your UI, we divide into A Model which mostly rerpresents our Data and Business-logic our View which consist of what we show the user in our UI and 
a Layer in-between the ViewModel. 

Another aspect is that it only allows a one-way interaction and updating the values in the other direction making it almost impossible to break things.

The ViewModel on one hand notifies our View of any changes, which itself Databinds the Properties of the ViewModel and uses Commands to interact. 
and on the other it updates the Model making each component absolutely independent.

Although we were provided with a [Tutorial concerning MVVM](https://learn.microsoft.com/en-us/dotnet/maui/tutorials/notes-mvvm/) I did not really follow along 
but rather used the Tutorial and some [YouTube Videos](https://youtu.be/5Qga2pniN78?si=O8S6GFniKhDeNM-e) to go along with my own Code for the UNDAC App.

## Code

As described above MVVM tries to separate our Code mostly three Components, which in our Code-Base are represented by the following:

* **Views** are represented by the ConentPages 
* **ViewModel** are represented by the newly introduced ViewModels
* **Model** are represented by our Models and Services 

### View 
Starting with the most basic our View is mainly represented by the XAML ContentPage.
```XAML
        <Label Text="Select Operational Team"></Label>
        <Picker ItemsSource="{Binding OperationalTeams}"
                ItemDisplayBinding="{Binding Name}"
                SelectedItem="{Binding SelectedOperationalTeam, Mode=TwoWay}"/>
```

We databind the our Components to the Properties of the ViewModel like with OperationalTeams in this case.
```XAML
            <Button Grid.Column="1"
                Text="Delete"
                Command="{Binding DeleteCommand}"/>
```
Or we call Commands to interact with our ViewModel like this DeleteCommand.
```cs
	public OperationRessourceRequestPage()
	{
		InitializeComponent();

		BindingContext = new OperationResourceRequestViewModel();
	}
```
We also must set our BindingContext accordingly.

### ViewModel

In the view Model we create instances of our Services to interact with our Model/Data.
```cs
private readonly IOperationResourceRequestService operationResourceRequestService; 
```

At the same time we create The Properties to notify our View of any changes:

```cs
		public OperationResourceRequest SelectedOperationResourceRequest
		{
			get => selectedOperationResourceRequest;
			set
			{
				if (selectedOperationResourceRequest != value)
				{
					selectedOperationResourceRequest = value;
					OnPropertyChanged(nameof(SelectedOperationResourceRequest));

					if (selectedOperationResourceRequest != null)
					{
						RequestedDetail = selectedOperationResourceRequest.RequestedDetail;
					}
				}
			}
		}
```

And assign Commands to allow the View to interact.
```cs
public ICommand DeleteCommand => new Command(Delete);
```
### Model

Lastly there is the model which for the most part consists of CRUD interfaces making requests to the Database (and normally of course the Model:

```cs
		Task<T> GetOne(string name);
		Task<List<T>> GetAll();
		Task<int> Add(T entity);
		Task<int> Update(T entity);
		Task<int> Remove(T entity);
		Task<bool> Exists(string name);
```

### Abstract Interface for Name Discriminator

The last Crud interface was also an example of a new abstract Interface to interact with tables which are build 
around a Unique Name attribute of which there are many instances in the overall EntityRelationship-Diagramm.

| ![week10_Name_Tables.png](https://github.com/byVirrox/portfolio_SET09102_Peter_Stibitz/blob/main/images/week10_Name_Tables.png?raw=true) |
|:--:|
| <b>Fig.1 - Instances of Tables mainly consisting of Name as a Primary Key</b>|

This is why I decided to make a new Abstract Model, which will hopefully simplify the creation of such Database Tables in the Future: 
```cs
using SQLite;
using System.ComponentModel;

namespace UndacApp.Models
{
	public abstract class ANameModel : INotifyPropertyChanged
	{
		private string name;

		[PrimaryKey]
		public string Name
		{
			get => name;
			set
			{
				if (name != value)
				{
					name = value;
					OnPropertyChanged(nameof(Name));
				}
			}
		}

		public event PropertyChangedEventHandler PropertyChanged;

		protected virtual void OnPropertyChanged(string propertyName)
		{
			PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(propertyName));
		}
	}
}

```



## Test Code 

My buddy did the [review](https://github.com/Software-Engineering-Red/MAUI-APP/pull/136) and has not found anything specific to criticize, except a Binding Error on my new OperationRessourceRequestPage.
Besides that, he passed my Pull Request through. 

I have not identified the source of this issue yet but will work on it as I continue my current UserStory.



# Review of my Buddy

My partner struggled this week as well a bit with finishing his Task, so I once again could not really give any advice or feedback.
I knew he had been out of town this week, so I did not want to stress him about that.

## Reflection

First of I struggled once again with writing proper tests for this week. General speaking, I could not really finish my User Story through the sheer number of tables that were missing around my End Goal.

As always, I was focused on splitting up my methods as small sized as I could and tried to apply the single Responsibility Principle.

I also tried to bring value to the project by making an abstract Class + Interface for the Service to contribute to the workflow, making it easier and faster for my Teammates to create new Tables with Name as PK.

But overall, I am pretty satisfied with my familiarisation with MVVM and applying it instantly to all the new Classes and ConentPages.
I really liked it, and it didn't even complicate my workflow but rather simplified it because the only thing I had to do different was to properly databind everything instead of always assigning the new values to the textFields, Pickers or entries.
So, although I did not achieve my initial goal it felt really productive.

And although I had some experience with MVVM through a Web-Dev Course at my home-university, this was the first time putting it into 