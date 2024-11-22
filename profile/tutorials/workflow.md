# Actual GitHub Workflow at GLFHC

## Install Git if not on your computer
if you don't have git on your computer, the easiest way to get started is to install [GitHub Desktop](https://github.com/apps/desktop), 
which is both a GUI client and includes the git tools.

## Existing Code never in GitHub
The safest way to move code into git is to create a new repository. When I do this I create it up on GitHub **first**, then clone
it to your drive (see instructions in [creating your first workflow](create_first_repo.md)), now copy your files into the 
directory of your project. If the project is python based, it is important to understand the directory structure of your project. Python
treats directories as "packages" and unlike Java which uses a hard identifier to link them, python uses folders. So if you name your project
something like "inventory_report" and the python folder is also "inventory_report" python will become confused. In the latest versions of python 3 (> 3.9)
this haphazard folder system was changed, before this you need a file named __init.py__ in a folder for python to understand it to be package
(packages can be imported). SQL of course has no such cares since it is not in fact a structured programming language and is used
for queries, and as such has no knowledge of file systems, but convention says start at the top folder of the repository.

