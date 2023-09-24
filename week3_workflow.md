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
* [Adding a databse](https://learn.microsoft.com/en-us/dotnet/maui/data-cloud/database-sqlite)

** Workflow ** 

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

 **Workflow in GitHub**

 In GitHub you can find several captions in the taskbar in the Repo of the Team. 
 One of them being the <ins>issues<ins>. Here you will find all tasks/issues.
 You can assign them to yourself by clicking onto one of the issues, which doesn't
 have a Profile Picture in the right corner, indicating it is already assigned to someone.
 Then you can assign it to yourself (or someone else) by clicking on the gear next to 
 assignee and selecting your account. 
 
 In my case this was the issue number #13 
 Futhermore	you should now create a Branch which is named accoring to the workflow convention.



The main requirements are to show that:

1. You know how to use the workflow tools in GitHub
2. You have successfully carried out the required operations which are:
   * Accept a task from the project backlog
   * Update the task information appropriately
   * Update the task board appropriately
   * Complete the development task on a feature branch
   * Commit your changes with appropriate comments
   * Check your work against the Definition of Done (DoD)
   * Make a pull request

## Task workflow

Here, you should use screenshots and descriptive commentary to show that the required
have been completed successfully.

**DO**

* Consider the layout of your content from the point of view of the reader. Many raw
  screenshots will take up a lot of space. It may be better to adopt a different strategy
  such as
  * Using thumbnails with links to full-size images
  * Combining two or more screenshots into a single image 
  * Using partial screenshots to highlight only the important information
  * etc.
* Provide links to the actual objects (e.g. task, pull request, etc.) in the team project
  in GitHub
* Read through your work to make sure that the information comes across clearly

**DON'T**

* Use humour or informal language
* Waste space with trivial or self-evident commentary
* Abbreviate your commentary using (e.g. by using bullet points). Your text should be in
  the form of grammatically correct sentences.

## Reflection

Here, you should highlight any difficulties that you faced in completing the task, and
how you resolved them.

You should also briefly discuss the current process and how it could be refined or
improved in future iterations. For example, is the DoD adequate or too onerous? Is the
current procedure for updating the task appropriate? Is the task board configured to
work in an intuitive way?
