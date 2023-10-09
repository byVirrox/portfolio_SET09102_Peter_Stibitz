# Documentation

Last week covered the principles of reviewing code and clean code.
In this section these principles will be applied and we will also cover Documentation.
This weeks three tasks were:

1. reviewing the code that had been written by yourself in week 3
2. generating a set of documentation for the project
3. reviewing someone else's code
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




https://github.com/Software-Engineering-Red/MAUI-APP/blob/develop/Documentation/doxygen.md
This section is related to your work on clean code and documentation in week 5.

First, choose six rules of clean code and explain them. For each one,

* Summarise the rule in your own words.
* Provide an example from the code that you wrote in week 2 and then refined in week 4.
* Explain how your code implements the rule. 

Second, copy the doxygen comments from your code into your portfolio and provide some 
descriptive commentary on their purpose and structure. Use screenshots showing the HTML 
content that is generated from your code to illustrate your explanation.

Finally, highlight three examples from your code where you have eliminated the need
for comments by adhering to the principles of clean code.

## Doxygen Commentary 

Our team 

## Eliminating the need of comments

Here I want to present tree examples how I eliminated the need of commentary 
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

