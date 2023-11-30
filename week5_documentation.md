# Documentation

Last week covered the principles of reviewing code and clean code.
In this section these principles will be applied and we will also cover Documentation.
This weeks three tasks were:

1. reviewing the code that had been written by yourself in week 3
2. generating a set of documentation for the project
3. reviewing someone else's code (we set up a buddy system)
4. updating the team documentation


## Clean Code

The first part of this will be a rewind of clean code rules, but this time how you would apply them to 
our MAUI-project and how they helped me improve my code.
In may case I worked on the issue of implementing a CRUD interface with UI for so called OperationalTeamStates:

### YAGNI 

Starting with an example from last week, YAGNI stands for "You Ain't Gonna Need It".

[To quote myself from a week ago](https://github.com/byVirrox/portfolio_SET09102_Peter_Stibitz/blob/main/week4_code_review.md):
"It is very common that some programmers especially when starting with a project
want to implement stuff halfway, before they even need it. You rather should 
design your system that you can extend it later on if needed but you should not
leave dead code behind in that process."

In this example I wrote a method to receive an instance of OperationalTeamStatus from the Database via the id:
```cs
        public async Task<OperationalTeamStatus> GetStatusAsync(int id)
        {
            await Init();
            return await _dbConnection.Table<OperationalTeamStatus>().Where(i => i.ID == id).FirstOrDefaultAsync();
        }
```
 
This originated from the database tutorial, but the issue was that I never used in the actual application.
Therefore you should always delete this sort of code.

### Using meaningful Names

It often happens that some module names are not self explanatory and the reader struggles more 
understanding the meaning of a single variable,than reading and understanding the actual code.
Therefore variables, classes methods etc. should always describe precisely but short what they are used for.

In my case I used an acronym at the beginning of a variable:


```cs
        ltv_states.ItemsSource = states;
```

Because I my self could not instantly recognize its meaning I renamed it via refactoring so
it actually gives me an idea of what it is. It is the ListView of all my OperationalTeamStatus (short statuses)

```XAML
            <ListView x:Name="list_view_states" ItemSelected="ltv_status_ItemSelected" Margin="20"
                      SelectionMode="Single">
```

```cs
list_view_states.ItemsSource = states;
 ```

### Function-Names

Another method of ensuring clean code, is checking your functions/methods for certain criteria.
Keep the function short, give it a verb as a name, use small numbers as parameters /not big ones,
provide only one functionality, don't have side effect (for example depend on the successful execution of previous code)
and keep it at a certain abstraction level, but don't include other levels into them.

In my case these principles almost all applied to a method except one. 
The name of it wasn't a verb, but rather describing the object, on which it is applied.

```cs
        private void ltv_status_Item(object sender, SelectedItemChangedEventArgs e)
        {
            selectedStatus = e.SelectedItem as OperationalTeamStatus;
            if (selectedStatus == null) return;

            text_editor_status.Text = selectedStatus.Name;
        }
```

Therefore I changed it to express to the reader what it does and what type of action is executed.

```cs
        private void select_Item_from_ListView(object sender, SelectedItemChangedEventArgs e)
        {
            selectedStatus = e.SelectedItem as OperationalTeamStatus;
            if (selectedStatus == null) return;

            text_editor_status.Text = selectedStatus.Name;
        }
```

### DRY

Another really self explanatory rule. Don't repeat yourself.
If code at two points like methods or classes seems similar, you should maybe find a way to 
outsource it and thereby join together these bits, so that you just have to refer to that new bit.
That could be done by writing a method for repeating parts of the code or maybe use inheritance.

This is no extreme case, but at the end of two classes I reset 3 variables the same way.

case 1:
```cs
    private async void DeleteButton_Clicked(object sender, EventArgs e)
    {
        ...

        selectedStatus = null;
        list_view_states.SelectedItem = null;
        text_editor_status.Text = "";
    }
```

case 2:
```cs
    private async void DeleteButton_Clicked(object sender, EventArgs e)
    {
    ...

        selectedStatus = null;
        list_view_states.SelectedItem = null;
        text_editor_status.Text = "";
    }
```

Instead of doing it that way I now made a method to do that. 
So in the other two cases I just have to call that method one time.
It not only takes care of repetition, but also is easier to read.

```cs
    private async void DeleteButton_Clicked(object sender, EventArgs e)
    {
    ...

        reset_SelectedStatus_and_Text()
    }
```



### Single Responsibility Principle

A method or class should only perform one specific task.
If they are doing more than that it might make sense splitting them up and encapsulating them
in smaller pieces. This is such an example:

The SaveButton_Clicked method not only checks every single case, but also 
sets up new values or creates a new status which makes this method way to complex.
(That is also an example of how to write fuctions short with one functionality or of 
the [KISS principal](https://github.com/byVirrox/portfolio_SET09102_Peter_Stibitz/blob/main/week4_code_review.md) referred to Last week because the code is not readable and way to complex)

```cs
    private void SaveButton_Clicked(object sender, EventArgs e)
    {
        if (String.IsNullOrEmpty(text_editor_status.Text)) return;

        if (selectedStatus == null)
        {
            var status = new OperationalTeamStatus() { Name = text_editor_status.Text };
            statusService.AddStatus(status);
            states.Add(status);
        }
        else
        {
            selectedStatus.Name = text_editor_status.Text;
            statusService.UpdateStatusAsync(selectedStatus);
            var status = states.FirstOrDefault(x => x.ID == selectedStatus.ID);
            status.Name = text_editor_status.Text;
        }


        selectedStatus = null;
        list_view_states.SelectedItem = null;
        text_editor_status.Text = "";
    }
```

Instead you can split these up into smaller functions, which only provide one functionality, 
but make the reading of this method much easier.

```cs
    private void SaveButton_Clicked(object sender, EventArgs e)
    {
        if (String.IsNullOrEmpty(text_editor_status.Text)) return;

        if (selectedStatus == null) 
        {
            createNewStatus();
        }
        else
        {
            updateStatus();
        }

        reset_SelectedStatus_and_Text();
    }
```

### Short Functions

As I mentioned earlier functions should be short only provide one functionality.
Therefore as I showed in my last example i encapsulated the three cases into their own private void methods:

```cs
    private void createNewStatus()
    {
        var status = new OperationalTeamStatus() { Name = text_editor_status.Text };
        statusService.AddStatus(status);
        states.Add(status);
    }

    private void updateStatus()
    {
        selectedStatus.Name = text_editor_status.Text;
        statusService.UpdateStatusAsync(selectedStatus);
        var status = states.FirstOrDefault(x => x.ID == selectedStatus.ID);
        status.Name = text_editor_status.Text;
    }
    
    private void reset_SelectedStatus_and_Text()
    {
        selectedStatus = null;
        list_view_states.SelectedItem = null;
        text_editor_status.Text = "";
    }
```

## Doxygen 

A big part of this weeks exercise was writing documentation in the for of comments.
Documentation is crucial to project due to maintenance. If you work long term on a project
things might get more complex, you might focus on other parts and forget about previous code or
you have to introduce new people to your codebase. 
For these cases we should take care of a persistent documentation.

Our team agreed on the following [procedure](https://github.com/Software-Engineering-Red/MAUI-APP/blob/develop/Documentation/doxygen.md) to comment our code for doxygen.
Part of the agreement is: 
commenting every sort of module like classes methods 
or instance variables so that it will show up in our [doxygen documentation](https://github.com/Software-Engineering-Red/MAUI-APP/tree/develop/Documentation/reference/html).

We commented our own code independently and 
I set up a doxygen configuration file for our team, let it generate the documentation and 
provided the [configuration file](https://github.com/Software-Engineering-Red/MAUI-APP/blob/develop/Documentation/doxygen_config/Doxyfile) for the team in the doxygen_config-folder
so that everyone is able to use the exact same configuration in case we want to make changes.

I will cover one example out of my classes:

**Navigation**

| ![week5_navigation.png](https://github.com/byVirrox/portfolio_SET09102_Peter_Stibitz/blob/main/images/week5_navigation.png?raw=true) |
|:--:|
| <b>Fig.2 - Overview of OperationalTeamStatusPage</b>|

The Navigation through the documentation takes place through the above tabs. 
Whereas **packages** has a folder overview, **class** provides differnt sorts of arragements 
of the classes, interfaces etc. For example odering them in a list, indexing them as a dictionary 
sorting them in a hierarchy or filtering.

**Overview**

| ![week5_doxygen_overview.png](https://github.com/byVirrox/portfolio_SET09102_Peter_Stibitz/blob/main/images/week5_doxygen_overview.png?raw=true) |
|:--:|
| <b>Fig.2 - Overview of OperationalTeamStatusPage</b>|

This doxygen page covers several aspects like a UML diagramm displaying the inheritance relation of the class,
private and public methods, attributes and amore concrete view on some of the functions.

**Inheritence-Diagram**

| ![week5_Inheritence_Diagram.png](https://github.com/byVirrox/portfolio_SET09102_Peter_Stibitz/blob/main/images/week5_Inheritence_Diagram.png?raw=true) |
|:--:|
| <b>Fig.3 - Inheritance diagram OperationalTeamStatusPage</b>|

OperationalTeamStatusPage extends the ContentPage interface.

**Functions**

| ![week_5_functions.png](https://github.com/byVirrox/portfolio_SET09102_Peter_Stibitz/blob/main/images/week_5_functions.png?raw=true) |
|:--:|
| <b>Fig.4 - Functions</b>|

This section gives an overview over all methods including the constructor in the public member functions or all private 
member functions. Under every Method header you can find a short summary that has been written in the code using XML elements 
according to the [Documentation comment specification of .NET](https://learn.microsoft.com/en-gb/dotnet/csharp/language-reference/xmldoc/).

**Attributes**

| ![week5_attributes.png](https://github.com/byVirrox/portfolio_SET09102_Peter_Stibitz/blob/main/images/week5_attributes.png?raw=true) |
|:--:|
| <b>Fig.5 - Attributes</b>|

Every attribute has short term description to provide information about their purpose and what they reference.

**Method Example**

| ![week5_Method_example.png](https://github.com/byVirrox/portfolio_SET09102_Peter_Stibitz/blob/main/images/week5_Method_example.png?raw=true) |
|:--:|
| <b>Fig.5 - Method Example</b>|

Last but not least the lower section covers more complex member functions. 
Therefore it also shows up additional info beyond the summary provided in the XML,
relating to parameters or return values.
In this case we only have void function so there is no additional info about return values.
But we get know the parameter **sender** and **e**´.

That's how the XML tags show up in the code commentary:

```cs
    /// <summary>
    /// Saves current Element of OperationalTeamStatus to Database
    /// unless string is empty. Creates new object if selectedStatus
    /// is currently null and adds it to the database
    /// through a clicking save button in the UI.
    /// </summary>
    /// <param name="sender">Object to be saved</param>
    /// <param name="e">UI Event triggering Method call </param>
    private void SaveButton_Clicked(object sender, EventArgs e)
    {
        if (String.IsNullOrEmpty(text_editor_status.Text)) return;

        if (selectedStatus == null) 
        {
            createNewStatus();
        }
        else
        {
            updateStatus();
        }

        reset_SelectedStatus_and_Text();
    }
```

## Eliminating the need of comments

Here I want to present three examples how I eliminated the need of commentary 
by designing and describing things self explanatory. Because I didn't have to make a lot of changes
I will rather refer to the examples I made in the previous section.


### Improved meaningful names

[Names](https://github.com/byVirrox/portfolio_SET09102_Peter_Stibitz/blob/main/week5_documentation.md#dry) play the most significant role in self explanatory code thereby 
acronyms should be avoided at any cost because other or you yourself might not 
identify their meaning if they are picked randomly. 
```cs
        ltv_states.ItemsSource = states;
```
With that said rather use full words that are readable.
In this case you can now spot this variable as databinding to ListView easier.
```cs
list_view_states.ItemsSource = states;
 ```

### Encapsulation
So this [example](https://github.com/byVirrox/portfolio_SET09102_Peter_Stibitz/blob/main/week5_documentation.md#dry) might be the most noticeable:

```cs
    private void SaveButton_Clicked(object sender, EventArgs e)
    {
        if (String.IsNullOrEmpty(text_editor_status.Text)) return;

        if (selectedStatus == null) 
        {
            createNewStatus();
        }
        else
        {
            updateStatus();
        }

        reset_SelectedStatus_and_Text();
    }
```

By just taking the pieces of that code, that already provided smaller functionalities, and
encapsulating them in methods, with short descriptive verb including names, everyone
can quickly recognize what the method does.

Reading it we see, that if in the case there is nothing yet selected we gonna create a new status element.
If some status is selected right now, we update the status and at the end in any case reset the text and the selection.
You could call this a summary of the methods previous functionality.

### Outsourcing

This also refers to a [previous example](https://github.com/byVirrox/portfolio_SET09102_Peter_Stibitz/blob/main/week5_documentation.md#dry):
```cs
    private void reset_SelectedStatus_and_Text()
    {
        selectedStatus = null;
        list_view_states.SelectedItem = null;
        text_editor_status.Text = "";
    }
```

Here we had two repetitions, which are now combined in one method.
Not only does it make the other two methods shorter, but it is also easier to read one 
bit of method than two.

