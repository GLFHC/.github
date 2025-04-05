# Team Sharing of SQL scripts
If you are on the BI team or the app team, you probably write a lot of SQL scripts and hopefully you share these, so what can you
do to make other people's lives easier with your code? This recipe consists of a few parts:

1. Saving and documenting changes over time
2. Make the project team workable
3. Automated checking of SQL code
4. Storing the script run commands

# Saving and documenting changes over time
In a team environment this is what github does for you, it handles teams sharing a common set of files.
Now you may ask why not just use sharepoint, which is a fair question, except sharepoint doesn't understand code and changes
and doesn't support the concept of multiple people simultaneously editing files. It also doesn't understand rollback and versioning.
For this part of the recipe there are two possibilities:

**A. You are the first member of the team**
1. Go to the [github GLFHC repository](https://github.com/GLFHC)
2. the green button on the right lets you _create a new repository_ Give it some sane name that works with your team's
   mission (there are some naming restrictions length, spaces, etc) But if they are the monthly accounting reports,
   consider "monthly_accounting_reports". Additionally to make the rest of us understand your project, create the
   README.MD (checkbox). If you want to be fancier, you can euse the GLFHC SQL project template (inside the new
   repository form select the template, this will add some SQL goodies we will talk about in a moment)
3. Once the repository is created, you need to clone the repository to your computer (clone is like checking out from a
   library, except unlike a
   real library, everyone gets a magical copy). Assuming you have the github desktop application you can select clone
   top right with the easy choice of "open in github desktop", you can also clone from inside the desktop app, when you
   select clone from the file menu one of the choices is github.com, and all your repositories are there.

**B. You are a member of an existing project that already has a repository**
1. This is the easiest recipe to follow
2. Go to the [github GLFHC repository](https://github.com/GLFHC)
3. Select your team's repository and select the clone button.  clone the repository to your computer (clone is like checking out from a
   library, except unlike a
   real library, everyone gets a magical copy). Assuming you have the github desktop application you can select clone
   top right with the easy choice of "open in github desktop", you can also clone from inside the desktop app, when you
   select clone from the file menu one of the choices is github.com, and all your repositories are there.

# Make the project team workable
You could of course dump every SQL script into the main directory of the repository, but remembering which is which will get ugly fast.
A repository is just a collection of files, so folders underneath work, so break your repository up by functions or developers. so in our monthly accounting projects:

```aiignore
monthly_accounting_reports/
        accounts_receivable/
            ar_monthly.sql
            ar_monthly_run.sh
            description.md
        accounts_payable/
            ap_monthly.sql
            ap_monthly_run.sh
            description.md
```
So in the above we have 2 sub projects or functions inside the monthly reports, one for AP and one for AR.
each one has a SQL script, runner script and markdown description file
Obviously this could be developer names as well, although remember if Mary leaves 2 years from now nobody is going to remember that
accounts recievable == Mary!

# Automated checking of SQL code
This part is optional, but never wrong to check for errors, if you used the template in **A2** above where the GLFHC SQL
project template was used, this will check your SQL syntax every time you checking. This does not check your
functionality but static code analysis of your SQL to make sure it is standards compliant. In this case
our template use sqlfluff to check the SQL code for conformance. This automatically will run on a commit.

# Storing the script run commands
One neat thing about Github (or git technically) is that it doesn't care what it is you want to checkin (other than size
limits) for instance you can check in word documents, powerpoints, etc. So checking in shell scripts or some code that
uses your SQL is easy.
Let's imagine for our recipe we will put a shell command to run our script:
so put that in a text file "_runmonthlyreport.sh_" or whatever with the text, and put it in the same folder 