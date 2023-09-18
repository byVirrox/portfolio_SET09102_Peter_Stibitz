# Setup

This section documents your practical work in week 2.

The main requirements are to show that:

1. Your development environment is correctly set up
2. You understand the setup including potential alternative configuration settings

This weeks praktical invoved setting up Visual Studio, a repository for TEAM RED
as well as this portfolio on GitHub as well as setting up a buddy system to
fill out possible knowlege gaps of the Team in the fields of C# or GitHub and familiarize 
with the scenario of UNDAC-Teams for the developement of an Application. 

(It should be mentioned that I am using Windows 10 as OS and steps might differ
in othe OS such as Mac or Linux Distributions)
 
 ** Setup Visual Studio **

 1. Head to [visualstudio.microsoft.com](https://visualstudio.microsoft.com/free-developer-offers/)
    to download the Installer for Visual Studio (Community Edition). It will serve as the IDE for our Project.
 2. Execute the .exe and go trough the installation process untill the Installer is 
    fully installed. 
 3. Now there should be an instance of Visual Studio Community 2022. For this project 
    we will be using MAUI when it comes to workloads. Futhermore in my case I changed 
    the lan
 4. After that it is either possible to directly clone 

** Setup Portfolio Repository ** 

  1. (This step can later on be skipped) Using the provided [template](https://github.com/edinburgh-napier/SET09102_portfolio/tree/main) 
     and copy the repository 
  2. Now clone the repository into a appropriate folder by either using VS with the provided 
     [https code](https://github.com/byVirrox/portfolio_SET09102_Peter_Stibitz.git) or cd to the folder in your console and use the command git clone 
     https://github.com/byVirrox/portfolio_SET09102_Peter_Stibitz.git 
  3. In case you used git clone, just open the folder in the starting Menu of VS

** Setup Team Repository ** 

    (After the creation Step 1-4 don't need to be repeated.
    I didn't set the Team Repository up, but here is a short description of one option to do it.
    Be aware there are more ways. You could for example also use git CLI or create first the rpo and than
    the MAUI-project and so on.)
   1. Go into VS, create a new Project and select .NET MAUI-App as the template
   2. There are many options but one would be to direct in cmd to
      the location of the project and use git init and add + commit the files.
   3. Go on GitHub and create a new empty repository
   4. In cmd cd now to the specified folder of your project and use 
    git remote add origin <REMOTE_URL>
# Sets the new remote
$ git remote -v
# Verifies the new remote URL
$ git push origin main
# Pushes the changes in your local repository up to the remote repository you specified as the origin

([source](https://docs.github.com/en/migrations/importing-source-code/using-the-command-line-to-import-source-code/adding-locally-hosted-code-to-github))
    
  5. In case you need to set it up elsewhere, again or on a team members system simply 
     Clone the Team-repository into a appropriate folder by either using VS with the provided 
     [https code](https://github.com/Software-Engineering-Red/MAUI-APP.git) or cd to the folder in your console and use the command 
     git clone https://github.com/Software-Engineering-Red/MAUI-APP.git
  6. In case you used git clone, just open the folder in the starting Menu of VS




## Environment configuration

Here, you should include appropriate screenshots with additional commentary. 


| ![Screenshot1.png](../images/Screenshot1.png) |
|:--:|
| <b>Image Credits - Fig.2 - 4K Mountains Wallpaper</b>|

* The current configuration is appropriate for developing a Application with MAUI.
  That is why the 
  but in case you would like to work with different languages/ frameworks/ Engines 
  you would need to install these in the Installer later on additionally. 
  (for Example Python, the Unity Engine, Azure, Node-JS, Linux in combination with C++ etc.) 



* There can be futher Tools obtained which will enhance the workflow. By going to Extensions
  and than searching the marketplace. These will not provide new features that add to
  the possible workload, but rather support you during the coding (for example Code-Completions
  for different languages)
**DO**

* Place your image files in the `images` folder
* Choose your screenshots carefully so that they communicate the appropriate information.
  Many screenshots with no clear purpose or with trivial differences are of little value.
* Provide <ins>descriptive</ins> commentary to explain why the screenshot is included. 
  what it shows and point out any particularly significant details.
* Modify your screenshots - for example, by adding arrows, outlines or other highlighting 
  techniques - to enhance their communication value.
* Remove unnecessary material from your screenshots such as toolbars, other windows and 
  computer desktop to eliminate distractions.
* Ensure that any important content is clearly legible. Pay particular attention to text
  size and image resolution.
* Use [numbered captions](https://towardsdev.com/3-ways-to-add-a-caption-to-an-image-using-markdown-f2ca30562be6) 
  as labels for images. Also, use the caption numbers to refer to images in your text.

**DON'T**

* Expect the reader to understand your reasons for including a screenshot. If you can't
  explain it, the screenshot will not do the job on its own.
* Include self-evident comments. For example, don't simply list the values used on a
  configuration page if those values are clearly visible in the screenshot.

## Reflection

For this section, decide what points are worth making and structure your content 
appropriately.


**Note to C#**

I don't have any experience with C# yet and rather come out of the Java-Cosmos.
That's why I partnered up with Zsombor from my Team. There are also two Chanells in our 
Discord-Server for support with C# and GitHub, where Team member can commit their 
questions and other Team members can help. 

** Dificulties with Setup **

I installed Visual Studio without the Workload of MAUI. Should that happen simply go back 
into the VS Installer and download the Pack and restart VS.


**DO**


* 
* Use sub-headings to differentiate between sections
* Provide <ins>reflective</ins> commentary that discusses, for example, limitations of
  your current configuration, how your configuration is appropriate for the current 
  project, alternative configuration that might be appropriate in ther circumstances, 
  etc.
* Mention any difficulties you had setting up your working environment and how you 
  resolved them

**DON'T**

* Repeat the descriptive commentary from point 1
* Give a blow-by-blow account of everything you did. Instead, you should highlight 
  the important points.
