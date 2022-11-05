# git

## Git Squash and remove previous commits

`$ git rebase -i HEAD~10  $ git push -u origin branch_name --force`

_#first command above: 
Retain the first commit from below( as pick) and change the rest of the `pick` to `squash`. 
Save the file. Now you can check which comment to keep. Here 10 shows last 10 commits in the local branch_

_#second command: force push is necessary_


_git log             # You can see all your commits squashes to one commit_

## How to remove/Undo a Commit from your PR
Suppose you've unwanted commit(pushed by mistake) with a large number of files and you want to remove that...

$ git rebase -i HEAD~n        #n stands for number of commits you need to view
                              # Replace pick with dropto “drop” the commit. 
                              # Don't touch anything else unless you feel highly daring at the time. Delete a commit by deleting that particular line.

$ git push -u origin branch_name --force          # force push is necessary

### Problem: SSL certificate problem: Unable to get local issuer certificate
#### Cause
This error occurs when a self-signed certificate cannot be verified. 

#### Workaround
Tell git to not perform the validation of the certificate using the global option:

```
git config --global http.sslBackend schannel

Or,

git config --global http.sslVerify false
```
**(warning) Please be advised disabling SSL verification globally might be considered a security risk and should be implemented only temporarily
**

### "Help, I keep getting a 'Permission Denied (publickey)' error when I clone/push!" 
_e.g. 
**git clone git@github.com:samirsahoo007/Linux.git** 
fails with permission denied error whereas 
**git clone https://github.com/samirsahoo007/Linux.git** 
works fine._

This means, on your local machine, you haven't made any SSH keys. Not to worry. Here's how to fix:

In terminal open **~/.ssh/id_rsa.pub** and copy the contents. Now open you github account Go to **Settings > SSH and GPG keys > New SSH** key Enter title and paste the key from clipboard and save it. Voila you're done.


**Notes** 
* Within the `.ssh` folder, there should be these two files: `id_rsa` and `id_rsa.pub`. These are the files that tell your computer how to communicate with GitHub, BitBucket, or any other Git based service. Your SSH keys must be named `id_rsa` and `id_rsa.pub` in order for Git, GitHub, and BitBucket to recognize them by default.

* To create the SSH keys, type `ssh-keygen -t rsa` and follow the instructions. This will create both `id_rsa` and `id_rsa.pub` files.

* Now that you've added your public key to Github and/or BitBucket, try to `git push` again and see if it works. It should!

More help available from [GitHub on creating SSH Keys](https://help.github.com/articles/generating-ssh-keys) and [BitBucket Help](https://confluence.atlassian.com/bitbucket/set-up-ssh-for-git-728138079.html).

# git branching
## Git Merge and Git Rebase

### Git Merge

The git merge command will merge any changes that were made to the code base on a separate branch to your current branch as a new commit.

The command syntax is as follows:

```
git merge BRANCH-NAME
```

For example, if you are currently working in a branch named dev and would like to merge any new changes that were made in a branch named new-features, you would issue the following command:

git merge new-features

**Note**: If there are any uncommitted changes on your current branch, Git will not allow you to merge until all changes in your current branch have been committed. To handle those changes, you can either:

Create a new branch and commit the changes

```
git checkout -b new-branch-name
git add .
git commit -m "<your commit message>"
```

Stash them

```
git stash               # add them to the stash
git merge new-features  # do your merge
git stash pop           # get the changes back into your working tree
```

Abandon all of the changes
```
git reset --hard        # removes all pending changes
```

### git rebase

Rebasing is often used as an alternative to merging. Rebasing a branch updates one branch with another by applying the commits of one branch on top of the commits of another branch. For example, if working on a feature branch that is out of date with a dev branch, rebasing the feature branch onto dev will allow all the new commits from dev to be included in feature. Here’s what this looks like visually:

          A---B---C topic
         /
    D---E---F---G master

From this point, the result of either of the following commands:

git checkout master
git rebase topic

would be:

                  A'--B'--C' topic
                 /
    D---E---F---G master

Note: It is more common to first checkout a branch and then run the rebase command with the name of the branch you wish to rebase on to.
otherwise this could have been **git rebase master topic**.


## Git Merge vs Git Rebase

Both git merge and git rebase are very useful commands, and one is not better than the other. However, there are some very important differences between the two commands that you and your team should take into consideration.

Whenever git merge is run, an extra merge commit is created. Whenever you are working in your local repository, having too many merge commits can make the commit history look confusing. One way to avoid the merge commit is to use git rebase instead.

git rebase is a very powerful feature. That being said, it is risky as well if it is not used in the right way. git rebase alters the commit history, so use it with care. If rebasing is done in the remote repository, then it can create a lot of issues when other developers try to pull the latest code changes from the remote repository. Remember to only run git rebase in a local repository.


## Typical Rebase Use Cases

### Updating a Feature Branch

Lets say you’re working away on a feature branch, minding your own business.

          A---B---C feature
         /
        E dev

Then you notice some new commits on dev that you’d like to have in your feature branch, since the new commits may affect how you implement the feature.

          A---B---C feature
         /
        E---F---G dev


You decide to run git rebase dev from your feature branch to get up-to-date with dev.

However when you run the rebase command, there are some conflicts between the changes you made on feature and the new commits on dev. Thankfully, the rebase process goes through each commit one at a time and so as soon as it notices a conflict on a commit, git will provide a message in the terminal outlining what files need to be resolved. Once you’ve resolved the conflict, you git add your changes to the commit and run **git rebase --continue** to continue the rebase process. If there are no more conflicts, you will have successfully rebased your feature branch onto dev.

                  A---B---C feature
                 /
        E---F---G dev

Now you can continue working on your feature with the latest commits from dev included in feature and all is well again in the world. This process can repeat itself if the dev branch is updated with additional commits.

### Updating a feature Branch Prior to Merge with dev branch
### Updating a Feature Branch Prior to Merge
iOdds are, when nearing the completion of a feature in a feature branch, the commit history could be improved upon by renaming some commits to something more appropriate, combining closely related commits, and/or reordering commits in a more logical order. Rebasing allows for all of this!

More??? Ref here: https://medium.com/osedea/git-rebase-powerful-command-507bbac4a234

## cherry-pick
Cherry picking is the act of picking a commit from a branch and applying it to another.

### Short example of situation, when you need cherry pick

Consider following scenario. You have two branches.

a) release1 - This branch is going to your customer, but there are still some bugs to be fixed.

b) master - Classic master branch, where you can for example add functionality for release2.

c) Team Collaboration - Suppose two of your team members are working on different parts of a bigger feature. One team member has created a common utility function/block of code which the other member also needs. The other team member can cherry-pick the commit where the code is present. This would allow the other team member to continue without “re-inventing the wheel”.

d) Bug Hotfixes - When a bug is discovered it is important to deliver a fix to end-users as quickly as possible. Let’s say your QA team finds a pre-existing bug that needs to be resolved ASAP. You are working on a feature branch and want to address this bug fix in your branch because it is related to the module you are working on. You can make an explicit commit that addresses the hotfix and then cherry-picks the commit directly to master.

e) Undoing changes and restoring lost commits - Sometimes a feature branch may go stale and not get merged into master. Sometimes a pull request might get closed without merging. Git never loses those commits and through commands like git log and git reflog they can be found and cherry-picked back to life.

NOW: You fix something in release1. Of course you need this fix also in master. And that is a typical use-case for cherry picking. So cherry pick in this scenario means that you take a commit from release1 branch and include it into the master branch.

### The steps to achieve this are as follows:

```
1. git checkout <name of branch you’d like to grab commit from>
2. git reflog                                     => If you don't know the commit; then use this command to figure out the commitid you want to cherry-pick to
3. git checkout <name of branch you would like to add commit to>
4. git cherry-pick <commit hash>             => Use the -x flag with <commit hash> when you are cherry-picking from a public branch otherwise do not use
5. git push origin <name of branch you would like to add commit to>
6. Create a PR with the base branch as master, merge it and you’re done.
```

## What does Git (master|REBASE 1/1) mean? How do I get rid of it?

You are stuck in the middle of a rebase.

If you have merged/solved conflicts for all the paths:
use git add . to commit resolved items.
use git rebase --continue to complete the process.

Or use git rebase --abort to exit the rebase process without any risk.

# Removing file from previous commit
In order to remove some files from a Git commit, use the “git reset” command with the “–soft” option and specify the commit before HEAD.

```
$ git reset --soft HEAD~1
```
When running this command, you will be presented with the files from the most recent commit (HEAD) and you will be able to commit them.

Now that your files are in the staging area, you can remove them (or unstage them) using the “git reset” command again.

```
$ git reset HEAD <file>
```
Note : this time, you are resetting from HEAD as you simply want to exclude files from your staging area

If you are simply not interested in this file anymore, you can use the “git rm” command in order to delete the file from the index (also called the staging area).

```
$ git rm --cached <file>
```
When you are done with the modifications, you can simply commit your changes again with the “–amend” option.

```
$ git commit --amend
```
To verify that the files were correctly removed from the repository, you can run the “git ls-files” command and check that the file does not appear in the file (if it was a new one of course)

```
$ git ls-files

<file1>
<file2>
```

You can even re-use the same commit message:
```
git commit -c ORIG_HEAD
```

# Squashing a commit

Squashing allows you to combine multiple commits in your branch's history into a single commit. This can help keep your repository's history more readable and understandable.

# git switch

The new 'experimental' **git switch branch** command is meant to provide a better interface by having a clear separation, which helps to alleviate the developers’ confusion when using git checkout.
One such example is **git checkout <filename>** which reverses the modifications of an unstaged file and **git checkout <branchname>** which switches branch.The dilemma is more when the filename and branchname are the same.
          
The new commands, **git restore** (takes care of operations that change file), and the **git switch** (takes care of operations that change branches) shares out the responsibilities of git checkout in a more intuitive way.
   
# Create pull request using GitHub CLI
## gh pr create
          
```
$ gh pr create --title "The bug is fixed" --body "Everything works again"
$ gh pr create --reviewer monalisa,hubot  --reviewer myorg/team-name
$ gh pr create --project "Roadmap"
$ gh pr create --base develop --head monalisa:feature
```

```
# Create a pull request using flags
~/Projects/my-project$ gh pr create --title "Pull request title" --body "Pull request body"
http://github.com/owner/repo/pull/1
~/Projects/my-project$
```

          
## Problem: GitHub: Clone succeeded, but checkout failed        
Ans: This is due to very long file name or semicolon in the file name. It can be solved either of below command.
```
          git config --system core.longpaths true
          
          OR
          
          git config --global core.longpaths true
```
         
          If the above doesn't work then use the following step:
          
If you are on windows, you should do:

Go to Computer Configuration → Administrative Templates → System → Filesystem in gpedit.msc , open Enable Win32 long paths and set it to Enabled.

After that git config --system core.longpaths true or edit gitconfig (you must run as administrator).

Open project directory with git bash and run git reset after that run git checkout *

## Git — you may want to set your merge.renamelimit variable to at least 1608 and retry the command.
```Auto-merging trunk/.config
CONFLICT (content): Merge conflict in trunk/.config
warning: inexact rename detection was skipped due to too many files.
warning: you may want to set your merge.renamelimit variable to at least 1608 and retry the command.
Automatic merge failed; fix conflicts and then commit the result.```

git config merge.renameLimit 99999
          
          
### [GIT] Fix error: RPC failed; curl 18 transfer closed with outstanding read data remaining
git config --global http.postBuffer 524288000


git clone http://github.com/large-repository --depth 1
cd large-repository
git fetch --unshallow


On Linux
Execute the following in the command line before executing the Git command:

export GIT_TRACE_PACKET=1
export GIT_TRACE=1
export GIT_CURL_VERBOSE=1
On Windows
Execute the following in the command line before executing the Git command:

set GIT_TRACE_PACKET=1
set GIT_TRACE=1
set GIT_CURL_VERBOSE=1


Disable the default 1GB limit of proxy_max_temp_file_size for Nginx. Value need to be set as zero to disable it
You can find more details here http://nginx.org/en/docs/http/ngx_http_proxy_module.html#proxy_max_temp_file_size . 
          
# Improving GIT Performance

You can improve git performance by doing the following:

> Limit the number of commits you have in a branch. You can squash commits or use an interactive rebase in which you add -s for each commit except the first one. By doing this you shall have one commit in a branch.

> Delete unused branches, also when merging a pull request, make sure you delete the branch afterward

> Truncate git history if commits earlier than a certain date are not needed, this will reduce clone repository time as less commits are fetched
          
          
# What is git rebase?

“Rebasing is the process of moving or combining a sequence of commits to a new base commit. Rebasing is most useful and easily visualized in the context of a feature branching workflow. "
          
# What does squashing commits mean?
By squashing commits if you have multiple commits in your branch you can combine them into one commit. This can easily be achieved by performing an interactive rebase. 
Let’s see how:

**Rebase to the rescue**

In order to sync your branch with the target branch you can get the latest version of the target branch from the server by doing a ‘git pull’ on that branch, then move to your branch and perform an interactive rebase.

If you are not interested in updating your local target branch you can achieve what is mentioned above in a single command (assuming that master is the target branch):

git pull origin master --rebase=interactive

It is important to understand that each time you perform a rebase the ids of the commits will be modified, so If you work with a couple of people on the same branch it is not recommended to use rebase as you will end up with commits with different ids but the same changes that will lead to potentially hard to solve merge conflicts.

If your work is located in a test-branch (the branch you will later merge in the target branch) and you want to rebase it with the latest version of master branch (the target branch), and also update your local master branch, you can execute the following commands:

```
git checkout master
git pull origin master
git checkout test-branch
git rebase -i master
```
          
This will open your text editor in which you can squash the commits:
```
pick f07702e First commit message.
s f58822dd Second commit message.
s 4709c5b Third commit message.
```
          
The result of this operation is one commit, you can customize its message, by default it is a concatenation of the messages from the three commits.

By executing the following command, you will notice that your commit is on top of the latest commit from master:
```
git log --oneline
```
After working with rebase instead of doing a git pull command to sync a branch with the target branch, there has been a noticeable improvement in git performance. More importantly the developers working on the same project started to get familiar with rebase and use it in day to day work. With our combined effort and by deleting unused branches we made our daily work easier as git commands were executed in a matter of seconds, instead of minutes.

Just keep one thing in mind while using rebase: the rebase command alters git history by changing the ids of the rebased commits, while this is ok for a local branch, for a branch that is pushed to a remote doing a rebase might lead in merge conflicts if other people are working on the branch. In this case it is recommended to do a rebase and squashing commits for the branch just before merge, when you know the work on that branch is completed.

**Delete unused branches**
When working with git, depending on the git workflow you use, there are one or more branches that have to be present on the git server. For example, if all the latest work is merged in the master branch, the master branch is present. If there are release branches that are created from master, then it is expected that release branches are present in git server as well.

These are examples of branches you expect to find in the git server. But how about branches that are no longer used and are present in the git server? Maybe branches for feature work or bug fixes that did not get deleted when their corresponding pull requests got merged. Or just branches that did not get merged at all in master as the pull requests for them got declined, for example.

The unused branches have a negative impact on overall git server performance. It was a team effort performed by the teams working on the project to determine all unused branches and to delete them. Let’s see how this was performed!

First sync your local git repo with the remote one:
```
git fetch --prune origin
```
Make sure you can run git in PowerShell, you only need to configure the PATH environment variable as described here https://thepracticalsysadmin.com/setting-up-git-in-windows/

Use the following PowerShell and bash scripts to get all merged and not merged branches and delete them: https://github.com/alinruscior/git-scripts/

If you are interested in getting all branches that contain a certain text in their name, let’s say ‘test’ you can use the ‘grep’ operator for searching:
```
git branch --remotes --no-merged master | grep "test"
```
A helpful script that gets all the merged and not merged branches in the ‘master’ branch together with details: last commit hash, date of last commit, author of last commit and writes the data in a csv (branches.csv) can be found here: https://github.com/alinruscior/git-scripts/blob/master/powershell/ExportLastCommitDetailsForAllBranches.ps

Bash script: https://github.com/alinruscior/git-scripts/blob/master/bash/ExportLastCommitDetailsForAllBranches.sh

Sample generated csv:



Truncate git history
We can reduce the number of commits in git by truncating the git history. In order to achieve this, we start a new branch from a certain commit and rebase the target branch in order to remove unwanted commits.

It’s worth mentioning that the file history will be altered by this process, meaning that in the first commit of the new truncated branch all files will be marked as created.

We can use the following command in order to export all the commits we have in a branch:

git log --format="%h %ai %an %s" --output=commits.txt

The commit hash, commit date, author, and commit message are displayed for each commit in the ‘commits.txt’ file. This can help you decide what is a good candidate for a starting commit in a new branch with truncated history.

The commands we could run are these:

git checkout --orphan temp $1 # create a new branch without parent history

 

git commit -m "Truncated history" # create a first commit on this branch

 

git rebase --onto temp $1 master -i # now rebase the part of master branch that we want to keep onto this branch 

 

git branch -D temp # delete the temp branch


## How to solve the following error?
$ git clone http://<my_url_prefix>/my_repo.git
Cloning into 'my_repo'...
remote: fatal: corrupt tree file
error: git upload-pack: git-pack-objects died with error.
remote: aborting due to possible repository corruption on the remote side.
fatal: early EOF
fatal: git upload-pack: aborting due to possible repository corruption on the remote side.
fatal: index-pack failed

### Solution
The error is possibly due to large repo or very large file in the repo. We can do the cloning as below.

$ git clone http://<my_url_prefix>/my_repo.git --depth 1 --branch my_branch
$ cd my_repo
$ git fetch --unshallow
$ git pull -p                        or                      git pull --all

## git "shallow clone + unshallow" vs "normal clone"
Considering the below two commands
1. 
git clone <url> --depth 1
git fetch --unshallow
and

2. 
git clone <url>
          
### Ans:
When you clone a very large repository, you need to transfer a lot of data. Depending on your network speeds, this may take a lot of time. To put in a few concrete numbers, let's use 10 GiB as the overall repository data-transfer size (data-transfer size and on-disk size will differ, though usually not by very much) and assume that you can get a transfer rate of 1 MiB/s. That means the data transfer takes 10240 MiB / (1 MiB/s) = 10240 s = 170.667 minutes = a bit under 3 hours (about 2 hours and 50 minutes).

The various protocols in use have error detection and (at the hardware level, often) correction built in, but there's still some chance the connection will fail during this period. If the connection does fail, git clone treats the whole thing as atomic: none of it worked, so Git will remove the entire clone.

If fetching with --depth 1 causes the initial clone to copy only, say, about 1/3 of the overall data, we drop our clone time to about 1 hour, dropping the risk of total failure as well. One can then incrementally add to the shallow clone (with --deepen or larger --depth numbers to git fetch). Each of these have their own risk of failure, but failure just results in adding no objects: the existing clone is unharmed. Retrying an hour's transfer as needed is less painful than restarting the entire 3-hour transfer only to have it fail 2 hours and 20 minutes in.1

Eventually, the final --unshallow gets you everything you would have gotten, had you been able to do a full clone all at once without error. Note that you may want to use --no-single-branch during the initial shallow clone, or fix up the fetch refspec after the initial shallow clone.

Is the final output of the clone the same for both?

The answer to this is both no and yes. We need to define what, precisely, we mean by "final output". In terms of useful access to all the commits and other objects, though, the results are the same (provided you undo the single-branch effect of the --depth argument).

If so, how is it that the second command works much faster than the first for a very large repo?

It doesn't necessarily work any faster. However, when doing a full clone, Git can—not necessarily does, but can—just send an existing pack file, rather than building a new one. This can save CPU time on the sending side. If the (single) pack file on the sender is well-constructed, the resulting single pack file on the receiver is also well-constructed. The result of doing repeated deepening with a shallow clone, or even a single unshallow, will normally be multiple pack files on the receiver; these may not be as well-constructed.

1I speak from actual experience here. :-) Transfer rates are higher now than they were over flaky DSL wiring in 2005, but repositories are larger now too. And, even now, the US has terrible internet infrastructure in some locations.
