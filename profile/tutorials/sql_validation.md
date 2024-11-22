# How to use GitHub actions to validate SQL syntax

## Usage
Most of the projects done at GLFHC are SQL scripts that run against one of the main databases.
This presents a problem for validation since obviously we can't post a copy of the database on GitHub to test
SQL code for validity, so we are left with static-code analysis (syntax checking) to make sure basic SQL rules are followed.

The tools we are describing here can validate embedded SQL code in other languages (such as python). let's take the two use cases:

### Pure SQL file
You have a SQL script file like this (this is MySQL dialect, but conceptually the same). For our test case here
we are going to use the [sqlfluff](https://docs.sqlfluff.com/en/stable/gettingstarted.html) tool (it requires python to run
but once python 3 is on your machine it can run against straight SQL files)

#### Example validation
here is our test.sql (malformed in this specific example since N and XXXX are undefined), note these static code analyzers are really picky, and often flag things
that are totally functional but technically not according to spec.

```mysql
SELECT id, stock_size, NDC, name, Strength, inventory_group_code, Inventory_group_name, inventory_on_hand, inventory_group_status, status, last_cost_paid, inventory_bin, Last_Supplier, medicationid
FROM mydb.inventory where N = XXXX;
```

Validate and output:
```text
sqlfluff lint test.sql --dialect mysql
== [test.sql] FAIL                                                              
L:   1 | P:   1 | LT05 | Line is too long (197 > 80).
                       | [layout.long_lines]
L:   1 | P:   1 | LT09 | Select targets should be on a new line unless there is
                       | only one select target. [layout.select_targets]
L:   1 | P:  24 | CP02 | Unquoted identifiers must be consistently lower case.
                       | [capitalisation.identifiers]
L:   1 | P:  35 | CP02 | Unquoted identifiers must be consistently lower case.
                       | [capitalisation.identifiers]
L:   1 | P:  67 | CP02 | Unquoted identifiers must be consistently lower case.
                       | [capitalisation.identifiers]
L:   1 | P: 171 | CP02 | Unquoted identifiers must be consistently lower case.
                       | [capitalisation.identifiers]
L:   2 | P:  30 | CP01 | Keywords must be consistently upper case.
                       | [capitalisation.keywords]
L:   2 | P:  36 | CP02 | Unquoted identifiers must be consistently lower case.
                       | [capitalisation.identifiers]
L:   2 | P:  40 | CP02 | Unquoted identifiers must be consistently lower case.
                       | [capitalisation.identifiers]
L:   2 | P:  44 | LT12 | Files must end with a single trailing newline.
                       | [layout.end_of_file]
All Finished 📜 🎉!
```

#### Install SQLFluff Tool
As we typically use Microsoft SQL server, you probably want to use the tsql dialect rather than MySQL.
to install this tool we use pip (or pip3 depending on the version of Python you have):

```text
pip3 install sqlfluff
```

#### What dialects does it support?
```text
% sqlfluff dialects

==== sqlfluff - dialects ====
ansi:                 ANSI dialect [inherits from 'nothing']
athena:            AWS Athena dialect [inherits from 'ansi']
bigquery:     Google BigQuery dialect [inherits from 'ansi']
clickhouse:        ClickHouse dialect [inherits from 'ansi']
databricks:    Databricks dialect [inherits from 'sparksql']
db2:                  IBM Db2 dialect [inherits from 'ansi']
duckdb:            DuckDB dialect [inherits from 'postgres']
exasol:                Exasol dialect [inherits from 'ansi']
greenplum:      Greenplum dialect [inherits from 'postgres']
hive:             Apache Hive dialect [inherits from 'ansi']
mariadb:             MariaDB dialect [inherits from 'mysql']
materialize:   Materialize dialect [inherits from 'postgres']                                                           
mysql:                  MySQL dialect [inherits from 'ansi']
oracle:                Oracle dialect [inherits from 'ansi']
postgres:          PostgreSQL dialect [inherits from 'ansi']
redshift:    AWS Redshift dialect [inherits from 'postgres']
snowflake:          Snowflake dialect [inherits from 'ansi']
soql:        Salesforce Object Query Language (SOQL) dialect
                                      [inherits from 'ansi']
sparksql:    Apache Spark SQL dialect [inherits from 'ansi']
sqlite:                SQLite dialect [inherits from 'ansi']
teradata:            Teradata dialect [inherits from 'ansi']
trino:                  Trino dialect [inherits from 'ansi']
tsql:         Microsoft T-SQL dialect [inherits from 'ansi']
vertica:              Vertica dialect [inherits from 'ansi']
```
Once you can run this properly locally on your machine it is time to add this as a task for GitHub to run every time you push code to the main branch.

When we look in the YAML file we built in [Using GitHub Automation](automation.md), we now need to add this functionality to our test
instance of ubuntu.

The first line to modify is 
```YAML
        python -m pip install flake8 pytest
```
to now read:
```YAML
        python -m pip install flake8 pytest sqlfluff
```
Remember this installation is not for **your** machine, it is the VM spun up at test/validate time on GitHub's cloud to run the test

the other line to modify from the example script is:
```YAML
        python -m unittest discover -s .
```

to now read (obviously change the file name (make sure indents match):
```YAML
       sqlfluff lint my_script.sql --dialect tsql
```
The first part tells Ubuntu to put the sqlfluff package on it's python instance (like most modern languages you can directly
execute a package like an application). The last part says to lint (linting is syntactic checking) the sql file using the dialect
rules for tsql. When this runs the status output will tell GitHub that it succeeded or failed, and change the badge.

### Embedded SQL in python
Embedding SQL in python, while somewhat frowned upon in enterprise applications (due to the risk of SQL injection if not _very, very careful_)
presents a validation challenge since the wrapping python can be totally legitimate python code but the SQL might be totally wrong. There is a package
in the PyPi library that can validate python wrapped SQL (again static analysis).

This package is helpfully called [sqlvalidator](https://pypi.org/project/sqlvalidator/), which can run both on the client and on GitHub.
For our example we will create a simple python class, and embed a query (faking all the session/connection stuff here for demo purposes).

**my_sql_test.py**
```python
from made_up_sql import SqlSession


class My_SQL_Test_Class

	session: SqlSession = None
 
	__init(self, session: SqlSession)__:
		self.sesssion = session
 

	def fun(self):
	    return self.session.query('''
			SELECT
			 firstname,
			 lastname
			FROM patients
			''')
```

In real life that code above would crash since the import would fail and there is no class called SqlSession.
But the point is the method **query_it** has SQL embedded in the text, we can use this next tool to not only validate it but
actually reformat the python to obey both SQL and PEP rules and save it back (the above where the query is on multiple lines was
done when I ran the tool)

#### Installing sqlvalidator
Like SqlFluff above, SqlValidator is a python package:

```text
% pip3 install sqlvalidator
```

Like SqlFluff you can run this against the python file (it will run against plain SQL but doesn't detect flaws as no python wrapper)

```text
% sqlvalidator --check-format my_sql_test.py

No file would be reformatted
```
(Yay!) if you wanted the tool to reformat your file for you (addition to checking it):

```text
% sqlvalidator --format my_sql_test.py
```
***IMPORTANT**:* this will modify your code in place! Make sure you want that!

#### Adding sqlvalidator to the project's automation. As above we are going to modify the YAML file:

You can choose to add this to the git on your machine as part of the commit. In your project, like we added a _.github/workflows_
folder, we can add one in the local project calls _.github/hooks_ which lets us put various hooks into the process.
So how can we add this hook? First create the hooks folder under .github, then create a YAML file called `.pre-commit-config.yaml`
Paste this inside (you will need the sha hash of the sqlvalidator repo in your script unless you just want to run with scissors and delete that
line:

```YAML
 - repo: https://github.com/David-Wobrock/sqlvalidator
    rev: <sha1 of the latest sqlvalidator commit>
    hooks:
      - id: sqlvalidator
```

Now when you get ready to commit your changes in git (regardless of what tool you use to manage git) this will be called first.
Errors will block the commit. 

The first line to modify is 
```YAML
        python -m pip install flake8 pytest
```
to now read:
```YAML
        python -m pip install flake8 pytest sqlvalidator
```

Now we need to add a step to run the validator

**Change this**
```YAML
        python -m unittest discover -s .
```

**to this** (obviously change the file name (make sure indents match):
```YAML
       sqlfluff lint my_script.sql --dialect tsql
```
