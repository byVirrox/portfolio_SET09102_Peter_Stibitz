# Setup

This weeks praktical invoved setting up Visual Studio, a repository for TEAM RED
as well as this portfolio on GitHub as well as setting up a buddy system to
fill out possible knowlege gaps of the Team in the fields of C# or GitHub and familiarize 
with the scenario of UNDAC-Teams for the developement of an Application. 

(It should be mentioned that I am using Windows 10 as OS and steps might differ
in othe OS such as Mac or Linux Distributions)
 
 **Setup Visual Studio**

 1. Head to [visualstudio.microsoft.com](https://visualstudio.microsoft.com/free-developer-offers/)
    to download the Installer for Visual Studio (Community Edition). It will serve as the IDE for our Project.
 2. Execute the .exe and go trough the installation process untill the Installer is 
    fully installed. 
 3. Now there should be an instance of Visual Studio Community 2022. For this project 
    we will be using MAUI when it comes to workloads. Futhermore in my case I changed 
    the lan
 4. After that it is either possible to directly clone 

**Setup Portfolio Repository** 

  1. (This step can later on be skipped) Using the provided [template](https://github.com/edinburgh-napier/SET09102_portfolio/tree/main) 
     and copy the repository 
  2. Now clone the repository into a appropriate folder by either using VS with the provided 
     [https code](https://github.com/byVirrox/portfolio_SET09102_Peter_Stibitz.git) or cd to the folder in your console and use the command *git clone 
     https://github.com/byVirrox/portfolio_SET09102_Peter_Stibitz.git*
  3. In case you used git clone, just open the folder in the starting Menu of VS

**Setup Team Repository** 

    (After the creation Step 1-4 don't need to be repeated.
    I didn't set the Team Repository up, but here is a short description of one option to do it.
    Be aware there are more ways. You could for example also use git CLI or create first the rpo and than
    the MAUI-project and so on.)
   1. Go into VS, create a new Project and select .NET MAUI-App as the template
   2. There are many options but one would be to direct in cmd to
      the location of the project and use $ git init and *$ git add .* and  *git commit -m "First commit"*.
   3. Go on GitHub and create a new empty repository
   4. In cmd cd now to the specified folder of your project and use 
* *git remote add origin <REMOTE_URL>*
* Sets the new remote
* *$ git remote -v*
* Verifies the new remote URL
* *$ git push origin main*
* Pushes the changes in your local repository up to the remote repository you specified as the origin

([source](https://docs.github.com/en/migrations/importing-source-code/using-the-command-line-to-import-source-code/adding-locally-hosted-code-to-github))
    
  5. In case you need to set it up elsewhere, again or on a team members system simply 
     Clone the Team-repository into a appropriate folder by either using VS with the provided 
     [https code](https://github.com/Software-Engineering-Red/MAUI-APP.git) or cd to the folder in your console and use the command 
     *git clone https://github.com/Software-Engineering-Red/MAUI-APP.git*
  6. In case you used git clone, just open the folder in the starting Menu of VS




## Environment configuration

These may give a clue what to look out for during the setup. 


| ![week2_SetupVS.png](https://github.com/byVirrox/portfolio_SET09102_Peter_Stibitz/blob/main/images/week2_SetupVS.png) |
|:--:|
| <b>Fig.1 - VS-Installer Workload Options</b>|

<ins>The current configuration is appropriate for developing a Application with MAUI.
  But in case you would like to work with different languages/ frameworks/ Engines 
  you would need to install these in the Installer later on additionally. 
  (for Example Python, the Unity Engine, Azure, Node-JS, Linux in combination with C++ etc.) </ins>

| ![week2_Extentions.png](https://github.com/byVirrox/portfolio_SET09102_Peter_Stibitz/blob/main/images/week2_Extentions.png) |
|:--:|
| <b>Fig.2 - VS-Extensions</b>|

<ins> There can be futher Tools obtained which will enhance the workflow. By going to Extensions
  and than searching the marketplace. These will not provide new features that add to
  the possible workload, but rather support you during the coding (for example Code-Completions
  for different languages). </ins>

| ![week2_GitHub_clone.png](https://github.com/byVirrox/portfolio_SET09102_Peter_Stibitz/blob/main/images/week2_GitHub_clone.png) |
|:--:|
| <b>Fig.3 - GitHub https</b>|

<ins>In almost any case you will find the necessary https URL to copy a repo under 
code, so you just have to copy it by clicking the copy button. </ins>

## Reflection


**Note to C#**

I don't have any experience with C# yet and rather come out of the Java-Cosmos.
That's why I partnered up with Zsombor from my Team. There are also two Chanells in our 
Discord-Server for support with C# and GitHub, where Team member can commit their 
questions and other Team members can help. 

**Dificulties with Setup**

I installed Visual Studio without the Workload of MAUI. Should that happen simply go back 
into the VS Installer and download the Pack and restart VS.

