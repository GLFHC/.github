# Actual GitHub Workflow at GLFHC

## Install Git if not on your computer
if you don't have git on your computer, the easiest way to get started is to install [GitHub Desktop](https://github.com/apps/desktop), 
which is both a GUI client and includes the git tools.

## Existing Code Which Currently is Not in GitHub
The safest way to move code into git is to create a new repository. When I do this I create it up on GitHub **first**, then clone
it to your drive (see instructions in [creating your first workflow](create_first_repo.md)), now copy your files into the 
directory of your project. If the project is python based, it is important to understand the directory structure of your project. Python
treats directories as "packages" and unlike Java which uses a hard identifier to link them, python uses folders. So if you name your project
something like "inventory_report" and the python folder is also "inventory_report" python will become confused. In the latest versions of python 3 (> 3.9)
this haphazard folder system was changed, before this you need a file named __init.py__ in a folder for python to understand it to be package
(packages can be imported). SQL of course has no such cares since it is not in fact a structured programming language and is used
for queries, and as such has no knowledge of file systems, but convention says start at the top folder of the repository.

## what to change in a template
There will be several standard files and directories in all GLFHC templates that we ask you use, these include:
- Documentation
- .github/workflows
- README.md

#### Documentation
As the name implies put documentation files in here, including say excel sheets that were used in creating the tooling,
and any items like this (or say you have an import data set in csv like the set of ICD-10 codes)
**Note**: There is a file size limit in GitHub that will prevent GitHub from processing a file, and while it can store it you will lose change tracking.

#### .github/workflows
This is the automation directory, as explained here, alter the YAML file within as specificied in [Using GitHub Automation](automation.md). We ask that
all GLFHC projects use automated testing and validation (to search for CVEs particularly, and assure others that the code is functional)

#### README.md
The readme is a special document in GitHub that displays when the repository is viewed on the website, and forms the documentation
for the entire repository. As seen in this repository for the tutorials, md files work like html (and in fact are cross-compiled into HTML on GitHub via PanDoc)
so you can link files like web pages. Please write useful information about the repository/project and anything you want to pass on
to others as to  what/why/how of your code. Also how your code is executed. Yes everyone understands that SQL code runs inside the database,
but at GLFHC what actually causes this (for instance a nightly job executes triggerd by time/file modification/etc.).

If your project uses some nifty tool or 3rd party (this applies more to Python or Java versus SQL) describe why and how you use it.
For instance on my projects I tend to really stronly believe on using ORMs rather than SQL in code (far more secure and self-validates) and 
since many people here are less attuned to that patter, I describe how the application uses the ORM (SqlAlchemy) to represent the datbaase.

## How to Commit
Have you been told you have a fear of commitment? Well that's not compatible with either SQL or git. Both require commits for somthing
to be persisted. But commit in git is a local phenomenon, in otherwords when you commit (**please, please put in a helpful commit message it drives a lot in the future**)
that just put it in the database locally on your machine as a persisted change (for you DBA types think of saving as a SQL INSERT within a transaction, until you commit that change is not in the main table
which is exactly true in git, but now that you comitted the transaction it's only local, to push it to GitHub's server, you need to Push Origin in your Git Tool).

## Autommated Testing
See the [automation page](automation.md) for details (this is a huge topic) as it describes how to 
modify the workflows YAML script to execute validation scripts

## Next
View [Adding Collaborators to the project](collaboration.md)