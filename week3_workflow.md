# Workflow

This weeks tasks consisted of several points:
1. Running trough two tutorials, which dealt with how 
   to create a MAUI-App and further how to integrate a SQLite Database into a project
   This section documents your practical work in week 3.
2. Work out a workflow across the team for our project and our Repository
3. Import a bunch of issues concerning the theme of UNDAC and interaction with the database.
4. Selecting one of these issues and completing it according to the team-workflow
5. (As a stretch goal implementing continuous integration for this Project)

**Tutorials**

In the first Tutorial a Notes-App was created, which enabled the user to
store several notes through saving the data locally in different files.
The second one gave ashort introduction to SQLite and how to integrate it into a project.
* [Notes Application](https://learn.microsoft.com/en-us/dotnet/maui/tutorials/notes-app/)
* [Adding a database](https://learn.microsoft.com/en-us/dotnet/maui/data-cloud/database-sqlite)


**Team-Workflow** 

Our workflow goes as following [link to the original](https://github.com/Software-Engineering-Red/MAUI-APP/blob/master/Documentation/workflow.md):

> #### When working on an issue
> - Move the issue to in progress, and assign to yourself
> - Make a feature branch with the naming convention feature/MAUI-APP#<your issue number>, push directly to there, NOT Develop or Master
> - Make all commit messages verbose, so the whole team can understand what you have added 
> 
> #### When moving your branch to Develop/Master
> - Ensure code is adequently commented and tested (if required)
> - When finished, your branch needs to have a Pull Request to move it into Develop, and again from Develop to Master. To avoid merge conflicts when merging your branch to Develop, ensure your branch is up-to-date with it BEFORE you make a Pull Request
> - When making a PR from your branch to Develop, or Develop to Master, ensure two people have reviewed your code, and follow up with any comments they have made before continuing. Additionally, click "squash commit" when completing a PR, to keep our repo clean 
> 
> #### Definition of Done (Kanban):
> - All acceptance criteria on issue are met
> - All integration tests/Unit tests are passed and/or written if required
> - All review comments have been correctly responded to and addressed if required
> - Procedure for updating a task: - Any additional information should be added in comments on the issue itself with as much detail as possible available to the assigned programmer

 **Workflow in GitHub as a case study**

 In GitHub you can find several captions in the taskbar in the Repo of the Team. 
 One of them being the <ins>issues</ins>. Here you will find all tasks/issues.
 You can assign them to yourself by clicking onto one of the issues, which doesn't
 have a Profile Picture in the right corner, indicating it is already assigned to someone.
 Then you can chose one and assign it to yourself. (In this case it is not necessary but you of course you should always chose the most urgent task)
 After that you should change it's status to in Progress.
 
 In my case this was the [issue number #14](https://github.com/Software-Engineering-Red/MAUI-APP/issues/14):
 > <details><summary>As a system administrator, I want to maintain reference values for operational team statuses</summary>
 >
 > **End user goal:**
 > To be able to list, create, update and delete reference values for operational team statuses
 >
 > **End business goal:**
 > To have appropriate statuses available to describe an operational team (e.g. 'requested', 'confirmed', 'active', etc.)
 > 
 > **Acceptance criteria:**
 >
 > * An operational team status item can be created, viewed, updated and deleted (CRUD functionality)
 >
 > **Measurement of success:**
 >
 > * Unit tests pass for all CRUD operations
 >
 > **Notes:**
 >
 > * The database table will be called `operational_team_status`
 > * The table will have a single column, `name`
 > </details>


 Furthermore you should now create a Branch, which is named according to the workflow convention (`feature/MAUI-APP#14`).
 Checkout on this branch and try to regularly make commits whilst working on your task.

 I will not go into detail about the process of working on the task.
 In summary I created the Class/Type *Operational Team Status*,
 after that created a UI with xaml interacting with a Service wich gives access to a local instance 
 SQLite and provides a CRUD-interface to save different instances of *Operational Team Status*.

 It can following the Acceptance Criteria create, update, view and delete
 an operational team status. (This is displayed down below)

 After that I set a Pull Request to merge into the `develop-branch`.

 Whilst it meets the Aceptance Critera it is missing an appropriate Unit-Test to meet the DoD yet.
 (*I will add it as soon as possible*) 

 **Commit Messages** 

 When it comes to commit Messages you should always keep an eye that they describe 
 what you have done in short terms but precisely.
 If I changed a function/method for a class I should provide info
 over what method i edited, where (in which class) and in the best case why and how.


## Task workflow

Here are a few examples provided to demonstrate some aspects of the workflow:

| ![week3_AssigneIssue.png](https://github.com/byVirrox/portfolio_SET09102_Peter_Stibitz/blob/main/images/week3_AssigneIssue.png) |
|:--:|
| <b>Fig.1 - Assigning an Issue</b>|

1. <ins>By clicking on the gear next to assignee you can assing the issue to someone out of your team</ins>
2. <ins>Through Labels you can provide futher information concernig that task. They can also be changed by the gear.</ins>
3. <ins>When picking a Task, you should always keep on updating your status to according to your progress:
   (The status can take teh following values: Todo, In Progress, In Review, Done)</ins>


| ![week3_CommitMessage.png](https://github.com/byVirrox/portfolio_SET09102_Peter_Stibitz/blob/main/images/week3_CommitMessage.png) |
|:--:|
| <b>Fig.2 - Example of a commit Message</b>|

<ins>
	A Commit Message should always be short so that you and your team members can read identify the right 
	commit in case your cherry picking or serching for the cause of an issue. Nevertheless the message should
	indicate cleary and precisly what has been done, where and maybe even why 
</ins>


| ![week3_Pull_Request.png](https://github.com/byVirrox/portfolio_SET09102_Peter_Stibitz/blob/main/images/week3_Pull_Request.png) |
|:--:|
| <b>Fig.3 - Example of my Pull Request</b>|

<ins>
	Pull-Request (PR) are there to ensure that more than one developer has read through the code before pushing/merging it to a develop Branch. 
	Otherwise issues can easyly go under and will sneak into the code of your colleagues. iT also prevents someone from accidentaly destroying
	something on the develop/ main branch which slows down the workflow.
</ins>

## Reflection

**Problems with C# and MAUI**

Although the syntax of Java and C# are pretty similiar,
I am not used working with Generics. I will have to read a bit more into that Part of C#.

Even though the tutorial provided a simpel introduction into MAUI I still feel like 
I am lacking some concepts. For example at one time I wasn't able to access another class because it was not in my namespace.
Also I still have no overview over xaml and the different UI-Elements and therefore oriented my UI on the MAUI-tutorial a lot.


**Problems with SQLite**

Overall I feel like I had the most struggle with SQLite. The tutorial provided didn't gave a short glimpse of one pattern 
you can implement quickly, but I struggeled adjusting it, so it suits my purpose. 
As well as with Generics, further reading will be necessary.

**Intuitive VisualStudio and GitHub**

I already worked with GitHub but up untill now never used Github internal Issues, but instead used Jira last Semester.
Therefore compared to Jira it was very easy to asses a task and change it's status

**Team communication** 

When talking about this issue I can't deny that I somewhat contribute to this problem, because I still feel a bit awkward talking in a different language after a long time,
but the team needs to use the Practical lessons more for Team internal communication instead of knowledge gathering. 
Many people started working on their task on the weekend. But a distribution of tasks could have taken place earlier.
It also felt like not everyone was included when creating the workflow, because most of it was agreed upon on discord,when could have easily been discussed in presence.