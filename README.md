# git

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
