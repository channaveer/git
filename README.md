GIT
===


1. LOCAL

    The working directory is a single checkout of one version of the project. These files are pulled out of the compressed database in the Git directory and placed on disk for you to use or modify.

2. STAGING
    
    The staging area is a simple file, generally contained in your Git directory, that stores information about what will go into your next commit. It’s sometimes referred to as the index, but it’s becoming standard to refer to it as the staging area.

3. GIT DIRECTORY / LOCAL REPOSITORY

    The Git directory is where Git stores the metadata and object database for your project. This is the most important part of Git, and it is what is copied when you clone a repository from another computer.

The basic Git workflow goes something like this:

- You modify files in your working directory.
- You stage the files, adding snapshots of them to your staging area.
- You do a commit, which takes the files as they are in the staging area and stores that snapshot permanently to your Git directory.


First-Time Git Setup
====================


git config that lets you get and set configuration variables that control all aspects of how Git looks and operates. These variables can be stored in three different places -

1. /etc/gitconfig file: Contains values for every user on the system and all their repositories. If you pass the option --system to git config, it reads and writes from this file specifically.

2. ~/.gitconfig file: Specific to your user. You can make Git read and write to this file specifically by passing the --global option.

3. config file in the Git directory (that is, .git/config) of whatever repository you’re currently using: Specific to that single repository. Each level overrides values in the previous level, so values in .git/config trump those in /etc/gitconfig.

Your Identity
-------------
The first thing you should do when you install Git is to set your user name and e-mail address. This is important because every Git commit uses this information, and it’s immutably baked into the commits you pass around:

    $ git config --global user.name "John Doe"
    $ git config --global user.email johndoe@example.com

Your Editor
-----------
Git uses your system’s default editor, which is generally Vi or Vim. If you want to use a different text editor, such as Emacs, you can do the following:

    $ git config --global core.editor emacs

Your Diff Tool
--------------
Another useful option you may want to configure is the default diff tool to use to resolve merge conflicts. Say you want to use vimdiff:

    $ git config --global merge.tool vimdiff

Git accepts kdiff3, tkdiff, meld, xxdiff, emerge, vimdiff, gvimdiff, ecmerge, and opendiff as valid merge tools. You can also set up a custom tool; see Chapter 7 for more information about doing that.

Checking Your Settings
----------------------
If you want to check your settings, you can use the git config --list command to list all the settings Git can find at that point:

    $ git config --list

    user.name=Scott Chacon
    user.email=schacon@gmail.com
    color.status=auto
    color.branch=auto
    color.interactive=auto
    color.diff=auto

check what Git thinks a specific key’s value is by typing git config {key}:

    $ git config user.name
    Scott Chacon

Git Basics - Getting a Git Repository
=====================================

Getting a Git Repository
------------------------
You can get a Git project using two main approaches.
1. The first takes an existing project or directory and imports it into Git.

2. The second clones an existing Git repository from another server.

Initializing a Repository in an Existing Directory
--------------------------------------------------
    $ git init

This creates a new subdirectory named .git that contains all of your necessary repository files. At this point, nothing in your project is tracked yet.

If you want to start version-controlling existing files (as opposed to an empty directory), you should probably begin tracking those files and do an initial commit. You can accomplish that with a few git add commands that specify the files you want to track, followed by a commit:

    $ git add *.c
    $ git add README
    $ git commit -m 'initial project version'

Cloning an Existing Repository
------------------------------
If you want to get a copy of an existing Git repository — for example, a project you’d like to contribute to — the command you need is git clone.

If you’re familiar with other VCS systems such as Subversion, you’ll notice that the command is clone and not checkout. This is an important distinction — Git receives a copy of nearly all data that the server has. Every version of every file for the history of the project is pulled down when you run git clone. In fact, if your server disk gets corrupted, you can use any of the clones on any client to set the server back to the state it was in when it was cloned (you may lose some server-side hooks and such, but all the versioned data would be there
    Syntax
    ------
    git clone [url]

    Eg:
    $ git clone git://github.com/schacon/grit.git

    Rename :
    $ git clone git://github.com/schacon/grit.git mygrit

That creates a directory named grit, initializes a .git directory inside it, pulls down all the data for that repository, and checks out a working copy of the latest version.

NOTE : Git has a number of different transfer protocols you can use. The previous example uses the git:// protocol, but you may also see http(s):// or user@server:/path.git, which uses the SSH transfer protocol. 

Git Basics - Recording Changes to the Repository
================================================
Each file in your working directory can be in one of two states: tracked or untracked. Tracked files are files that were in the last snapshot; they can be unmodified, modified, or staged. Untracked files are everything else — any files in your working directory that were not in your last snapshot and are not in your staging area. When you first clone a repository, all of your files will be tracked and unmodified because you just checked them out and haven’t edited anything.

Untracked           Unmodified             Modified             Staged


    Add the file
---------------------->
    
                            Edit the file
                    --------------------------->

                                                    Stage file
                                            ------------------------>

                                    Commit
                    <-------------------------------------------------

    Remove the file
<-------------------

Checking the Status of Your Files
---------------------------------
To determine which files are in which state is the git status command.

    $ git status
    On branch master
    nothing to commit, working directory clean

This means you have a clean working directory — in other words, no tracked files are modified.

Add new file README

    $ vim README
    $ git status
    On branch master
    Untracked files:
      (use "git add <file>..." to include in what will be committed)

            README

    nothing added to commit but untracked files present (use "git add" to track)

You can see that your new README file is untracked, because it’s under the “Untracked files” heading in your status output.

Tracking New Files
------------------
    $ git add README
    $ git status
    On branch master
    Changes to be committed:
      (use "git reset HEAD <file>..." to unstage)

            new file:   README

The git add command takes a path name for either a file or a directory; if it’s a directory, the command adds all the files in that directory recursively.

Staging Modified Files
----------------------
If you change a previously tracked file called benchmarks.rb and then run your status command again, you get something that looks like this:

    $ git status
    On branch master
    Changes to be committed:
      (use "git reset HEAD <file>..." to unstage)

            new file:   README

    Changes not staged for commit:
      (use "git add <file>..." to update what will be committed)
      (use "git checkout -- <file>..." to discard changes in working directory)

            modified:   benchmarks.rb

The benchmarks.rb file appears under a section named “Changes not staged for commit” — which means that a file that is tracked has been modified in the working directory but not yet staged. To stage it, you run the git add command (it’s a multipurpose command — you use it to begin tracking new files, to stage files, and to do other things like marking merge-conflicted files as resolved). Let’s run git add now to stage the benchmarks.rb file, and then run git status again:

    $ git add benchmarks.rb
    $ git status
    On branch master
    Changes to be committed:
      (use "git reset HEAD <file>..." to unstage)

            new file:   README
            modified:   benchmarks.rb

Both files are staged and will go into your next commit. At this point, suppose you remember one little change that you want to make in benchmarks.rb before you commit it. You open it again and make that change, and you’re ready to commit. However, let’s run git status one more time:

    $ vim benchmarks.rb
    $ git status
    On branch master
    Changes to be committed:
      (use "git reset HEAD <file>..." to unstage)

            new file:   README
            modified:   benchmarks.rb

    Changes not staged for commit:
      (use "git add <file>..." to update what will be committed)
      (use "git checkout -- <file>..." to discard changes in working directory)

            modified:   benchmarks.rb

What the heck? Now benchmarks.rb is listed as both staged and unstaged. How is that possible? It turns out that Git stages a file exactly as it is when you run the git add command. If you commit now, the version of benchmarks.rb as it was when you last ran the git add command is how it will go into the commit, not the version of the file as it looks in your working directory when you run git commit. If you modify a file after you run git add, you have to run git add again to stage the latest version of the file:

    $ git add benchmarks.rb
    $ git status
    On branch master
    Changes to be committed:
      (use "git reset HEAD <file>..." to unstage)

            new file:   README
            modified:   benchmarks.rb

Ignoring Files
--------------
Often, you’ll have a class of files that you don’t want Git to automatically add or even show you as being untracked. These are generally automatically generated files such as log files or files produced by your build system. 

you can create a file listing patterns to match them named .gitignore. Here is an example .gitignore file:

    $ cat .gitignore
    *.[oa]
    *~

The first line tells Git to ignore any files ending in .o or .a — object and archive files that may be the product of building your code. The second line tells Git to ignore all files that end with a tilde (~), which is used by many text editors such as Emacs to mark temporary files. You may also include a log, tmp, or pid directory; automatically generated documentation; and so on.

The rules for the patterns you can put in the .gitignore file are as follows:

- Blank lines or lines starting with # are ignored.
- Standard glob patterns work.
- You can end patterns with a forward slash (/) to specify a directory.
- You can negate a pattern by starting it with an exclamation point (!).

Global patterns are like simplified regular expressions that shells use. An asterisk (*) matches zero or more characters; [abc] matches any character inside the brackets (in this case a, b, or c); a question mark (?) matches a single character; and brackets enclosing characters separated by a hyphen([0-9]) matches any character in the range (in this case 0 through 9) .

Eg:
    # a comment - this is ignored
    # no .a files
    *.a
    # but do track lib.a, even though you're ignoring .a files above
    !lib.a
    # only ignore the root TODO file, not subdir/TODO
    /TODO
    # ignore all files in the build/ directory
    build/
    # ignore doc/notes.txt, but not doc/server/arch.txt
    doc/*.txt
    # ignore all .txt files in the doc/ directory
    doc/**/*.txt

A **/ pattern is available in Git since version 1.8.2.

Viewing Your Staged and Unstaged Changes
----------------------------------------
You want to know exactly what you changed, not just which files were changed — you can use the git diff command.

 Let’s say you edit and stage the README file again and then edit the benchmarks.rb file without staging it. If you run your status command, you once again see something like this:

    $ git status
    On branch master
    Changes to be committed:
      (use "git reset HEAD <file>..." to unstage)

            new file:   README

    Changes not staged for commit:
      (use "git add <file>..." to update what will be committed)
      (use "git checkout -- <file>..." to discard changes in working directory)

            modified:   benchmarks.rb

To see what you’ve changed but not yet staged, type git diff with no other arguments:

    $ git diff
    diff --git a/benchmarks.rb b/benchmarks.rb
    index 3cb747f..da65585 100644
    --- a/benchmarks.rb
    +++ b/benchmarks.rb
    @@ -36,6 +36,10 @@ def main
               @commit.parents[0].parents[0].parents[0]
             end

    +        run_code(x, 'commits 1') do
    +          git.commits.size
    +        end
    +
             run_code(x, 'commits 2') do
               log = git.commits('master', 15)
               log.size

NOTE : That command compares what is in your working directory with what is in your staging area. The result tells you the changes you’ve made that you haven’t yet staged.

Use git diff --staged, which may be easier to remember.) This command compares your staged changes to your last commit:

    $ git diff --cached
    diff --git a/README b/README
    new file mode 100644
    index 0000000..03902a1
    --- /dev/null
    +++ b/README2
    @@ -0,0 +1,5 @@
    +grit
    + by Tom Preston-Werner, Chris Wanstrath
    + http://github.com/mojombo/grit
    +
    +Grit is a Ruby library for extracting information from a Git repository

It’s important to note that git diff by itself doesn’t show all changes made since your last commit — only changes that are still unstaged. This can be confusing, because if you’ve staged all of your changes, git diff will give you no output.

For another example, if you stage the benchmarks.rb file and then edit it, you can use git diff to see the changes in the file that are staged and the changes that are unstaged:

    $ git add benchmarks.rb
    $ echo '# test line' >> benchmarks.rb
    $ git status
    On branch master
    Changes to be committed:
      (use "git reset HEAD <file>..." to unstage)

            modified:   benchmarks.rb

    Changes not staged for commit:
      (use "git add <file>..." to update what will be committed)
      (use "git checkout -- <file>..." to discard changes in working directory)

            modified:   benchmarks.rb

Committing Your Changes
-----------------------
From Staging -> Commit

The files that are untracked will not be committed. Only the files that are tracked and which are in staging will be able to commit it to the local git repository.

    $ git commit
    The above command opens the default text editor to enter the message of the commit.
Doing so launches your editor of choice, although you can configure it with whatever you want using the  
    
    git config --global core.editor

Or else even you can commit with message inline in the command line

    $ git commit -m "Your commit message"

Skipping the Staging Area
-------------------------
If you want to skip the staging area, Git provides a simple shortcut. Providing the -a option to the git commit command makes.

IMPORTANT :  Git automatically stage every file that is already tracked before doing the commit, letting you skip the git add part

    $ git status
    On branch master
    Changes not staged for commit:
      (use "git add <file>..." to update what will be committed)
      (use "git checkout -- <file>..." to discard changes in working directory)

            modified:   benchmarks.rb

    no changes added to commit (use "git add" and/or "git commit -a")
    $ git commit -a -m 'added new benchmarks'
    [master 83e38c7] added new benchmarks
     1 files changed, 5 insertions(+)

Removing Files
--------------
To remove a file from Git, you have to remove it from your tracked files (more accurately, remove it from your staging area) and then commit. The git rm command does that and also removes the file from your working directory so you don’t see it as an untracked file next time around.

f you simply remove the file from your working directory, it shows up under the “Changes not staged for commit” (that is, unstaged) area of your git status output:

    $ rm grit.gemspec
    $ git status
    On branch master
    Changes not staged for commit:
      (use "git add/rm <file>..." to update what will be committed)
      (use "git checkout -- <file>..." to discard changes in working directory)

            deleted:    grit.gemspec

    no changes added to commit (use "git add" and/or "git commit -a")

 If you run git rm, it stages the file’s removal:

    $ git rm grit.gemspec
    rm 'grit.gemspec'
    $ git status
    On branch master
    Changes to be committed:
      (use "git reset HEAD <file>..." to unstage)

            deleted:    grit.gemspec

The next time you commit, the file will be gone and no longer tracked. If you modified the file and added it to the index already, you must force the removal with the -f option. This is a safety feature to prevent accidental removal of data that hasn’t yet been recorded in a snapshot and that can’t be recovered from Git.

IMPORTANT :

Another useful thing you may want to do is to keep the file in your working tree but remove it from your staging area. In other words, you may want to keep the file on your hard drive but not have Git track it anymore. This is particularly useful if you forgot to add something to your .gitignore file and accidentally staged it, like a large log file or a bunch of .a compiled files. To do this, use the --cached option:

    $ git rm --cached readme.txt

You can pass files, directories, and file-glob patterns to the git rm command. That means you can do things such as

    $ git rm log/\*.log

Note the backslash (\) in front of the *. This is necessary because Git does its own filename expansion in addition to your shell’s filename expansion. On Windows with the system console, the backslash must be omitted. This command removes all files that have the .log extension in the log/ directory. Or, you can do something like this:

    $ git rm \*~

This command removes all files that end with ~.

Moving Files
------------
Renaming the file in GIT

    $ git mv file_from file_to

    $ git mv README README.txt
    $ git status
    On branch master
    Changes to be committed:
      (use "git reset HEAD <file>..." to unstage)

            renamed:    README -> README.txt

However, this is equivalent to running something like this:

    $ mv README README.txt
    $ git rm README
    $ git add README.txt

Git Basics - Viewing the Commit History
=======================================

Viewing the Commit History
--------------------------
After you have created several commits, or if you have cloned a repository with an existing commit history, you’ll probably want to look back to see what has happened.

    git clone git://github.com/schacon/simplegit-progit.git

    $ git log
    commit ca82a6dff817ec66f44342007202690a93763949
    Author: Scott Chacon <schacon@gee-mail.com>
    Date:   Mon Mar 17 21:52:11 2008 -0700

        changed the version number

    commit 085bb3bcb608e1e8451d4b2432f8ecbe6306e7e7
    Author: Scott Chacon <schacon@gee-mail.com>
    Date:   Sat Mar 15 16:40:33 2008 -0700

        removed unnecessary test code

    commit a11bef06a3f659402fe7563abf99ad00de2209e6
    Author: Scott Chacon <schacon@gee-mail.com>
    Date:   Sat Mar 15 10:31:28 2008 -0700

        first commit

IMPORTANT:
Git log list all the commits in the descendign order of the commits made.

    $ git log -p -2

which shows the diff introduced in each commit. You can also use -2, which limits the output to only the last two entries

You can show the git diff in single line with the option -U1 and even can compare word wise with the following command
    
    $ git log -U1 --word-diff

 if you want to see some abbreviated stats for each commit, you can use the --stat option:

    $ git log --stat

    $ git log --stat
    commit ca82a6dff817ec66f44342007202690a93763949
    Author: Scott Chacon <schacon@gee-mail.com>
    Date:   Mon Mar 17 21:52:11 2008 -0700

        changed the version number

     Rakefile |    2 +-
     1 file changed, 1 insertion(+), 1 deletion(-)

    commit 085bb3bcb608e1e8451d4b2432f8ecbe6306e7e7
    Author: Scott Chacon <schacon@gee-mail.com>
    Date:   Sat Mar 15 16:40:33 2008 -0700

        removed unnecessary test code

     lib/simplegit.rb |    5 -----
     1 file changed, 5 deletions(-)

    commit a11bef06a3f659402fe7563abf99ad00de2209e6
    Author: Scott Chacon <schacon@gee-mail.com>
    Date:   Sat Mar 15 10:31:28 2008 -0700

        first commit

     README           |    6 ++++++
     Rakefile         |   23 +++++++++++++++++++++++
     lib/simplegit.rb |   25 +++++++++++++++++++++++++
     3 files changed, 54 insertions(+)

The --stat option prints below each commit entry a list of modified files, how many files were changed, and how many lines in those files were added and removed. It also puts a summary of the information at the end.

--pretty
This option changes the log output to formats other than the default. The oneline option prints each commit on a single line, which is useful if you’re looking at a lot of commits. In addition, the short, full, and fuller options show the output in roughly the same format but with less or more information, respectively:

    $ git log --pretty=oneline
    ca82a6dff817ec66f44342007202690a93763949 changed the version number
    085bb3bcb608e1e8451d4b2432f8ecbe6306e7e7 removed unnecessary test code
    a11bef06a3f659402fe7563abf99ad00de2209e6 first commit

The most interesting option is format, which allows you to specify your own log output format. This is especially useful when you’re generating output for machine parsing — because you specify the format explicitly, you know it won’t change with updates to Git:

    $ git log --pretty=format:"%h - %an, %ar : %s"
    ca82a6d - Scott Chacon, 11 months ago : changed the version number
    085bb3b - Scott Chacon, 11 months ago : removed unnecessary test code
    a11bef0 - Scott Chacon, 11 months ago : first commit

--graph  option adds a nice little ASCII graph showing your branch and merge history, which we can see in our copy of the Grit project repository:

    $ git log --pretty=format:"%h %s" --graph
    * 2d3acf9 ignore errors from SIGCHLD on trap
    *  5e3ee11 Merge branch 'master' of git://github.com/dustin/grit
    |\
    | * 420eac9 Added a method for getting the current branch.
    * | 30e367c timeout code and tests
    * | 5a09431 add timeout protection to grit
    * | e1193f8 support for heads with slashes in them
    |/
    * d6016bc require time for xmlschema
    *  11d191e Merge branch 'defunkt' into local

Git Basics - Undoing Things
===========================

Undoing Things
--------------
At any stage, you may want to undo something. 

Changing Your Last Commit
-------------------------
One of the common undos takes place when you commit too early and possibly forget to add some files, or you mess up your commit message. If you want to try that commit again, you can run commit with the --amend option:

    $ git commit --amend



