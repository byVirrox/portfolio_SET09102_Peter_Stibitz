# Project work 4

## Introduction

This week I continued working on the User Story from last time, reviewed three Pull Requests and tried to use queries with sqlite.net extension.

Due to the fact that I was not really succesfull with my query I will report what I have learned about that framework and mainly focus on my reviews
this week. 

## User Story

The User Story I continued this week was ([Issue#83](https://github.com/Software-Engineering-Red/MAUI-APP/issues/83)). It was about assigning Resources such as food, medical Supplies etc. to Operations.
The Acceptance criteria consisted of:
1. ~~Creating Filters and clearing them~~
2. Sort the Operations and highlight those with new Requests
3. Approving the Requests 
4. Showing details of the Operations
5. Showing all of the Request for a Operation

I now finished a Page to display Operations which can be filtered now. I had to rwork some aspcets, because a Team Mate did change something on one specific Model and its corresponding contentPage which took a a loot of time to restore.

## SQLite.Net Extension and ORM

ORM ([Object relational Mapping](https://en.wikipedia.org/wiki/Object–relational_mapping)) is based on the idea treating a Database just like Objects inside your code, creating a virtual object database.

In our architecture we use lazy initialisation for each table, which means that a corresponding Service initilises a table if it does not already exist.

In Theory it it should be really convenient treating a database just like Objects, but in Practice one might often encounter problems.

Especially implementing foreign Keys seems hard, because we normally do not really know anything about other tables because they are threated by seperate Sevices.

Normal Sqlite.net does therefore not support Foreign keys and relationships between differnt tables. To get such behavior we need to use [Sqlite.net Extension](https://github.com/media-tools/sqlite-net-extensions).

It makes it possible to provide one-to-one, one-to-many, many-to-one, many-to-many, inverse and text-blobbed relationships in our database.

In my case I wanted to make it possible to create a Query from the Operation Table, to obtain all OperationalResourceRequests by simply joining these two tables with the inbetween table OperationalTeam.

| ![week11_ERD.png](https://github.com/byVirrox/portfolio_SET09102_Peter_Stibitz/blob/main/images/week11_ERD.png?raw=true) |
|:--:|
| <b>Fig.1 - ERD with Relationship I try to map</b>|

I had a few attemps, ending up trying to acces the List of the certain objects which Sqlite.net extension requires to implement [One to Many Relationships](https://github.com/media-tools/sqlite-net-extensions#one-to-many).

Unfortunatly this only returns null, which suggests that I might have done some kind of mistake implemeting it.

## Code

```cs
		private bool checkForRequest(Operation operation)
		{
			if (operation.OperationalTeams == null)
				return false;
			if (operation.OperationalTeams.Any(team => team.OperationResourceRequests == null))
				return operation.OperationalTeams.Any(team => team.OperationResourceRequests.Count != 0);
			return false;
		}
```
This was the described attemt. All other options which ChatGPT suggested with join() methods wouldn't compile.
I will try to find a solution up until next week.


Like every week I of course worked on improving a certain Level of Code Quality. This week I implemented a blueprint abstract class for my ViewModels so that it's easier to implement a standard ContentPage which creates, updates , deletes and displays certain Objects.
```cs
	public abstract class AStandardViewModel : INotifyPropertyChanged

	{

		public ICommand SaveCommand => new Command(Save);
		public ICommand DeleteCommand => new Command(Delete);
		protected abstract void Save();
		protected abstract void Delete();

		protected abstract Task LoadData();

		protected abstract void add();

		protected abstract void update();
		protected abstract void resetValues();

		protected abstract void remove();

		protected virtual void OnPropertyChanged(string propertyName)
		{
			PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(propertyName));
		}

		public event PropertyChangedEventHandler? PropertyChanged;
	}
```
I planned on using dependency injection for a varing number of Services, but because these are based on an abstract Interfaces that was not possible.

## Test Code 

The problem causing My Testproject not to compile remains. One of the next Merges might fix it.


## Review of my Code
Since I am currently stuck with my query I decided not to make a Pull-Request as unfinished Code would only cuase confusion. 
Following the workflow I will merge as often as possible to prevent merging conflicts.

## Code Reviews

Afer last week not reviewing Code of my buddy, this Time I had the occasion reviewing three Pull Request. I will shorly summarise each of them and point things out that might need some improvement or some things that I thought were really good.

### Code Review 1

My buddy worked on the following [Issue #85](https://github.com/Software-Engineering-Red/MAUI-APP/issues/85):

```cs
...
if (teamMember != null)
            {
                teamMember.AccessPrivilegeLevel = selectedRequest.PrivilegeLevel;
                await memberService.Update(teamMember);
                selectedRequest.Approved = true;
                await requestService.UpdatePrivilegeRequest(selectedRequest);
                requests.Remove(selectedRequest);
                await requestService.DeleteRequest(selectedRequest);


                ltv_privilegeRequests.ItemsSource = requests;
            }

...
```
One critique was that he should outsource large sections that also violate the single Responsibility Principle and could easily be outsourced into a seperate method, enhancing the code quality.
```cs
        if (selectedTeamMember == null && int.TryParse(txe_privilegeLevel.Text, out privilegeLevel) == true)
        {
...
            if (pickerSystemType.SelectedItem == null)
            {
...
            }
            else if (teamMember.IsSystemTypeUpdatePending)
            {
...
            }
        }

        else
        {
            if (int.TryParse(txe_privilegeLevel.Text, out privilegeLevel) == true)
            {
                selectedTeamMember.AccessPrivilegeLevel =
                if (selectedTeamMember.IsSystemTypeUpdatePending)
                {
...

                if (selectedTeamMember.AccessPrivilegeLevel.Equals("disabled") || privilegeLevel <= int.Parse(selectedTeamMember.AccessPrivilegeLevel))
                {
...

                }


                else
                {
...
```

Aother point was the code complexity of this section which included the previous problem aswell. These sections could be ordered and inbetweencode should be outsourced to sub-methods with speaking names to gurantee for readability.

### Code Review 2
This PR followed right after. Therefore I recognised the same pattern again. I will include a few other examples, but most of my critique once again consisted of the same thing, which is outsourcing methods for readability and to ensure that methods only serve one purpose.

```cs
   public interface ILocalMediaService

    {  
        Task<List<LocalMedia>> GetLocalMediaList();
        Task<int> AddLocalMedia(LocalMedia localMedia);
        Task<int> DeleteLocalMedia(LocalMedia localMedia);
        Task<int> UpdateLocalMedia(LocalMedia localMedia);
    }
```
He implemented a whole interface although we have an abstract CRUD-Interface IService, which makes it a lot easier working and interacting with each of the services.
He aswell implemnted the whole service, which also could have been done by using the Abstract service AService.

```XAML
            <Picker.Items>
                <x:String>Press</x:String>
                <x:String>TV</x:String>
                <x:String>Radio</x:String>
            </Picker.Items>
```
One Last critique was that he hardcoded a selection which should normally use the Database.

### Code Review 3

This was a review for another Team Member. He maily focused on deleting and replacing old Services with our new abstract generic Service.
This was overall just really beneficial allowing us now to work with the same Interface for all Models and making our CodeBase more manageable.
## Reflection

As I described at the beginning I had most of my struggles with SQLite.net and its extension. 

Moreover I had to rewrite a piece of code, because a Team member overwrote it with his own Implementation. This normally no problem, but sometimes it is importatnt to communicate such changes. 
(But to defend this Team mate, he did not know I was still working on my Issue and its overall hard to communicate such small issues in such a large Team.)

I definetly feel like I have improved in giving feedback on Code, through trying to workon my own mistakes in the last weeks and steadily improving.
Therefore I am now more mindful and recognize these exact same mistakes faster. Especially if someone could transform some sort of section into a submethod or when it is more convenient to use Abstract classe generic Interfaces etc..

Although I have improved I still feel like I repeat way to much code, that feels similar which also is linked to the project and the structure of MAUI. 

I will have to find solutions to the problems I encountered till next week and hopefully finish my User Story. Moreover I would of course like to practice testing, as I have been saying this for weeks.

This means I will have wide selection of topics for next week to cover.