# Starting out

## Check that we have git 

```
    $ git --version 
```

## Getting help

```
    $ git 
```

by itself returns most common commands. Same as:

    $ git help 

You get an extended list of commands by doing:

    $ git help --all

Want to know more about a command you can do:

    $ git help add

which is the same as:

     $ git add --help

## Set up a local repository

Git will complain if you do not set:

    $ git config --global user.name "Mario Antonioletti"
    $ git config --global user.email "mario@epcc.ed.ac.uk"

Can also set:

    $ git config --global color.ui "auto"
    $ git config --global core.editor "nano"

You can see what configuration parameters you have 
set by doing:

    $ git config -l

Putting entries into:

    $ ls ~/.gitconfig

## Setting up a proxy

You may have to create a proxy if your organisation
does not allow you to directly go into the internet:

     $ git config --global http.proxy proxy-url
     $ git config --global https.proxy proxy-url

to unset:

     $ git config --global --unset http.proxy
     $ git config --global --unset https.proxy

## Creating a repository:

     $ mkdir planets
     $ cd planets

Check what is there:

     $ ls -A

Initialise the repository:

     $ git init

Check to see what has changed:

     $ ls -A

Check the status of the repository.

     $ git status

## First commit

Edit a file (use an editor you are familiar with):

     $ vi mars.txt

Could add:

 > Cold and dry, but everything is my favourite colour.

Check the state:

     $ ls

Check the file contents:

     $ cat mars.txt

and what is the repository status?

     $ git status

Workflow

    working directory --> staging area --> git repository
           ^                                     |
           |                                     |
           +-------------------------------------+

Add the file to the staging area:

    $ git add mars

Let's check the status:

     $ git status

Suppose you made a mistake and you want to unstage:

     $ git rm --cached mars.txt

Check:

     $ git status

## First commit

     $ git commit -m"Starting to think about Mars."

     [master (root-commit) 18e86a7] Starting to think about Mars.
      1 file changed, 2 insertions(+)
      create mode 100644 mars.txt

Check the status again:

     $ git status

If you do not specify the "-m" flag it will fire an editor
for you.

     $ git log

Can have a shortened version of the commit log

     $ git log --oneline

Modify the text file:

 > Cold and dry, but everything is my favourite colour.
 > Wonder if there are martians there?
 
 Check the contents:
 
     $ cat mars.txt

and the status:

    $ git status

Suppose you can't remember what you changed, you can do:

    $ git diff

If we add the file to the staging area:

    $ git add mars.txt

If you do:

    $ git diff

You can not see the difference because the file has been 
staged. However, you can do:

    $ git diff --staged

Make more changes to show that there is a difference 
between what is committed comes from the staging area
and not the working copy.

## Miscellaneous worth knowing

If you commit with a typo you can change:

    $ git commit -amend
    

## Exploring history

Can do a diff with earlier versions:

    git log --oneline
    
pick as specific revision and do:

    git diff RevNum mars.txt
    
Can short hand this by:

    git diff HEAD~N mars.txt
    
where the `N` signifies how many revisions back you want to go
and `HEAD` points to the current version of the repository.

If you want the specific contents of a file at given revision
you can do:

    git show RevNum mars.txt
    
You make modifications to a file that you no longer want to keep:

    $ git checkout -- mars.txt

Or you can even checkout earlier versions of the code:

    $ git checkout RevNum -- mars.txt
    
If you want to recover the state of the repository you can go back
to `HEAD` by doing:

    $ git reset --hard HEAD
    
## Ignoring files

You have seen that `git status` is a really useful command to 
show you what has been changed. However if you have files that
you do not want to put in the repository such as emacs back up 
files, binary files, etc you can tell git to ignore these by
creating a `.gitignore` file. You can create this file and 
add content that will apply recursively:

     *~
     .\#*
     \~*
     bin/
     
add and commit this file and you will reduce the clutter you get.

    $ git add .gitignore
    $ git commit -m".gitignore: files to ignore."
    
git will not let you commit files that are contained in the `.gitignore` file.

# Collaborating

You should have set up a bit bucket account - we use bit bucket because it provides private repositories by default but there are several others you can use:

* [BitBucket](http://www.bitbucket.org) 
* [GitHub](http://www.github.com)
* [GitLab](http://www.gitlab.com)

We shall:

1. Go to [http://www.bitbucket.org](http://www.bitbucket.org).
2. Login (if you have not created an account you will have to sign up).
3. Choose `Create a Repository`.
4. Choose a name for your repository `Planets`.
5. Choose `I have an existing project`.
6. Follow the instructions there.
7. Click on `Overview` on the left hand menu and you should see your file together with the commits.
8. click on source and you should be able to browse your sources.

Now we going to do something drastic:

    rm -fr planets

We shall take a copy of the remote repository:

    git clone https://mariotest@bitbucket.org/mariotest/planets.git Person1
    
If you go into the repository you should see that all your history is there:

    cd planets
    git log --oneline
    
Now want you to pair up.

1. One of you will open up your repository to the other.
2. Person 1 click on the `Share` button.
3. Type in their BitBucket username.
4. Make sure you give them write permissions.
5. Person 2 should get an email and see Person 1's repository on their dashboard.
6. Person 2 should clone the repository.

Note you can clone giving the repository a name:

    git clone https://mariotest@bitbucket.org/mariotest/planets.git Person2
    
Now person 2 will change the `mars.txt` file and then add and commit:

    $ git add mars.txt
    $ git commit -m"mars.txt: updated the contents."

But this change has only registered in the local repository. We need to 
push it to the remote repository:

    $ git push
    
Now person 1, in order to see these changes will have to pull the changes,
in order to register these in their repository:

    $ git pull
    
and the changes should be merged. Now person 1 and 2 add a line to the mars file.

 > I will miss my favourite fruit -- pears -- in Mars
 
Try to make sure that the fruit is different and you are in the same location. Then:

    $ git add mars.txt
    $ git commit -m"mars.txt: added my favourite fruit."
    
 Person 1:
 
    $ git push 
    
 Person 2:
 
    $ git push
    
 This will fail because person 1 has pushed their data before you did. You
 have to do a `git pull` before you can push:
 
    $ git pull
 
But now you find you have a conflict - you have both tried to edit the same line and `git` does not know how to resolve the problem. You will have to edit the file, resolve the conflict (talk to the other person!!):

    $ git add
    $ git commit -m"mars.txt: resolved the conflict."
    $ git push
    
Person 1 should be able to pull the file and together you can enter into a happy collaboration.

# On branching

Branching allows a very powerful way of forking your code. Suppose you want to 
instrument it for performance analysis or you are collaborating and you do not
want to be modifying the same code at the same time. Unlike CVS and SVN branches
are really cheap and uncomplicated.

     $ git branch
     * master
     
describes what branches are available. At this stage you will only have one
branch available `master`. We can easily create a new branch:

     $ git branch dev
     $ git branch
       dev
     * master

You can switch between branches:

     $ git checkout dev
     $ git branch
     * dev
       master
       
You can use the usual `git` workflow.

Create a file `earth.txt` on the `dev` branch, add and commit.

      $ git checkout master
      $ ls

There is no `earth.txt` but we can import the contents from the 
the `dev` branch to the `master` branch by doing:

       $ git merge dev
       
You may have to resolve conflicts but this is a very powerful way
of working.

# To finish off

We want to clone the Edinburgh bootcamp content. This is on [GitHub](http://www.github.com). We did not start using GitHub because they do not offer free
private accounts by default. GitHub have a slightly different approach to doing
things - you `fork` code which is a clone at the server end.

1. Go to [https://github.com/hpcarcher/2014-12-03-edinburgh](https://github.com/hpcarcher/2014-12-03-edinburgh)
2. You have a choice:
   i. Fork the repository and then clone
   i. Clone the repository as is (but you will have no write access)

    
    
