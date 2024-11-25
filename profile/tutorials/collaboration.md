# Sharing a repository

## Adding other contributors to your Repository
As your mother told you, _sharing is caring_! And that is true in GitHub as in life, but it is critical
to understand how GitHub shares repositories.

## Never Share Respository Files Directly
Never, ever simply copy a respository on one machine to another, this will create potention serious errors
in repositories (including time-travel where files seemingly travel through time before they were modified)

## Adding A User
Assuming you are the owner of a repository (or have been granted privileges to administer the repository) adding another 
collaborator is easy. At the top of the repository select _Settings_:

![GitHub Settings](images/github_settings.png "GitHub Settings")

### Collaborators And Teams
Next select _Collaborators And Teams_ on the left:

![GitHub Collaborators](images/collaborator_link.png "GitHub Collaborators")

### User Access Management
Manage the access on this screen

![GitHub Acess Management](images/manage_access.png "Manage Collaborators")

On this screen you see 2 different ways to manage access, first off is via roles, now everyone in the GLFHC Dev groups has 
general **read-only** access to repositories unless you use the idividual access tools below to specify individuals.
For individual access, you can either create a team of users, and then add them to the repository as a single team (very helpful
if a team will be sharing multiple projects), or can use the **Add People** button to add a specific person to the repository.
When you add someone, it is strongly (like seriously!) recommended to use the person's email address within GLFHC or if they are 
added with another email address you need to absolutely make sure it is the same person, allowing access to a non GLFHC person
would permit a signficant security breach. Once you select the person, as you see below, you need to decide on what access
they need to the repository:

### User Privileges
![GitHub User Privileges](images/user_privs.png "GitHub User Privileges")

For basic co-developers generally that is "_write_" privs, Maintain is for people who might be doing somewhat higher level 
tasks (such as changing structure or actions of the repository), Admin is essentially root access of the repository, and generally
there should be only one admin in general (or you get clashing of changes potentially) Anyone who has upper level admin access,
such as editing of the GLFHC group in general, can get in to a repository if something gets broken.

## Next
View [Managing Non-Code Files in GitHub](noncode_files.md)
