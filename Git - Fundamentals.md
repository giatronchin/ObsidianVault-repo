# Git
#git #github #gitkraken

To initialize a repository within a folder_project:
```bash
git init
```

Every **editing, creation, deletion or renaming** of file within a repository is seen as a change from the git previous status of the repository. To check what are the last changes since the last commit:
```bash
git status
```

To **stage** some of these changes or a portion of them we have to use the _add_ command:
```bash
git add changed_file_path
```

To make **git ignore tracking on specific file** just add them to the `.gitignore` file.

To **stop tracking changes on file already in track** (committed/added at least once):
```bash
git update-index --skip-worktree file.txt
```
That will ignore changes to that file, both local and upstream, until you decide to allow them again with:
```bash
git update-index --no-skip-worktree file.txt
```

You can get a list of files that are marked skipped with:
```bash
git ls-files -v . | grep ^S
```

To **commit** the staged changes to the local repository:
```bash
git commit -m "Commit message"
git commit -a -m "Add to stage & commit command"
```

To **amend** to the last previous commit issue:
```bash
git commit --amend -m "New Commit message"
```

Commit types include:
| Commit Type | Description |
| --- | --- |
| Feat |Refers to a feature|
| Fix|Refers to a bug fixe|
| Docs|Changes to documentation like README|
| Style|Style or formatting change|
| Perf|Improves code performance|
| Test|Test a feature|

#### Diff
To compare 2 different dataset between them:
-  2 different _commits_
-  2 different _files_
-  2 different _branches_

#### Revert
Revert will "undo" a previously issued commit.
This can be done by refering to a SHA specific commit value or referencing ordered commit within a branch:
```bash
git revert <commit> 
git revert main ~2
```
_Last command revert to the commit that is 2 commit-behind the head commit(last-one) in the main branch_.

By default, the `git revert` command will open a promptin the GitKrakenCLI to edit your Git commit message. 
If you don’t wish to change your message, you can skip right over thatstep by including the `--no-edit` flag before the gitrevision.
If you need to make additional code changes before you undo the Gitcommit, you will use the `-n` flag (you can also use `--no-commit`).

### Branch
**Pointer** to a specific commit in time. Branches get shift when issuing a commit off a previous branch base.

The branch pointer moves along with each new commit you make, and only diverges in the graph if a commit is made on a common ancestor commit.
![diverging branches](https://files.cdn.thinkific.com/file_uploads/702905/images/1b7/cd8/395/branch-pointer.gif?width=1920)

If you want to **create a Git branch** using the terminal, you will use the `git branch` command, followed by your desired **branch name**.
```bash
git branch <branch-name>
```

To **rename a Git branch** locally using the terminal, you will use `git branch -m` followed by the desired **new branch name**.
```bash
git branch -m <branch-new-name>
```

To **delete a Git branch** using the terminal, you’re going to want to use the `git branch -d` command along with the name of the local branch you want to delete.
```bash
git branch -d <branch-to-delete-name>
```

This Git command will show you remote branches. The `-r` flag here is short for `--remotes`.
```bash
git branch -r
```

This Git command displays not only the names of remote repositories, but their reference information, including Git commit hash.

```bash
git ls-remote
```

### Checkout
The Git checkout command tells Git to which branch or commit you want your changes applied. If you want to make changes to a branch that you don’t already have checked out, you will first need to checkout the branch.
Checking out a Git branch will update your repository’s files to match the snapshot of whichever commit the branch points to.

To view a list of your local branches (* will make the checked-out branch in your local repo):

```bash
$ git branch
* dev
  production
```

To checkout a different branch or even a specific commit (which does have a branch pointer):
```bash
git checkout <branch-name>
git checkout -b <new-branch-name>
git checkout <commit-hash>
```

To retrieve a commit's hash you can issue:
```bash
git log
git log --pretty=online
```

To rename a branch:
```bash
git branch --move <new-branch-name>      #rename current checkout branch
git branch -m <branch-name> <new-branch-name> #rename a target branch without checking it out first
```

To delete a local branch (checkout to another branch first!):
```bash
git branch -d <branch-name>
git branch -D <branch-name> #delete it no matter unstaged changes it has 
```

To delete a remote branch:
```bash
git branch -r #list all remote branch available
git push <remote-repository-name> --delete <remote-branch-name>
```

#### Git Detached HEAD

It’s important to be aware: checking out a Git commit will move the HEAD point to a commit instead of a branch. This will put your repository in what’s called **detached HEAD state**.
When you are in detached HEAD state in Git, you are not able to commit changes to a branch.

### Merging
As expected this feature merge content of two branches.
* preserve history
* better for merge conflicts
* easy to undo

To merge 2 different branch (all pending change has to be commited before merge):
```bash
git merge <branch-name> #merge current checkout branch with a target branch
```

Conflict emerge during merging/rebasing/cherry-picking two branches. When different change apply to the same line code in two different dataset git arise a conflict issue. Same occur with missing/deleted/created file differences in compared datasets.

![conflicts](https://blog.axosoft.com/wp-content/uploads/2019/04/conflicts.gif)

>Merging preserves a more complete history, and makes it easier to undo mistakes

### Rebase
Rebase move all issued commit on branch to another (sort of append commit to another branch). Essentially, Git rebase is deleting commits from one branch and adding them to another.
* cleaner history
*  more readable graph
*  tougher to resolve conflicts

To rebase 2 different branch:
```bash
git rebase <target-branch> #rebase current checkout branch with a target branch
```

>Commits don't have to be squashed before performing a rebase

#### Interactive Rebase
Allow to perform a specific task on each of the commit we are about to rebase before this take place:
+ Pick - place the commit as it is
+ Reword - change commit message
+ Squash
+ Drop - remove the commit
+ Reorder

Conditions:
- No merge commits present
- Common ancestor commit
- Neither branch has initial commit
- Rebase branch is not a parent branch

### **Git Pull Rebase vs Git Pull Merge**

Git pull merge is the default method for combining changes in Git, and will merge the unpublished changes with the published changes, resulting in a merge commit.

With Git pull rebase, on the other hand, the unpublished changes will be reapplied on top of the published changes and no new commit will be added to your history.

```bash
git pull --rebase
```

### Remote, Forks and Pull Request

Remote: remote repository over the internet
Cloning: create a local copy of a remote repository. This copy is attached with the remote and can be synchronized with it by **pulling/pushing** changes from/to it.

Pull is the act of download the lastet version of the remote repository to the local machine. It is the combination of two action **fetch** and **merge**. 

```bash
git pull <remote-repository>
```

```bash
git fetch <remote-repository>
```

#### Pull Request
Contributor can issue **pull request** to merge their contribution code to a main (remote) repository.
1.  Fork project
2.  Code
3.  Pull request toward main repo

#### Fork
Create a copy of a project without affecting it with every single commit.

### Stashing
Temporarly, safe mode to save ("stash") local change we are not yet ready to commit.
```bash
git stash   #stash current changes without commit them
git stash show #see the content of the current stash
git stash list #show list of all local machine stash
git stash show stash@{1} #show content of stash #1
```

* Apply - apply stashed changes to the local repository commiting them. This will retain the stash and its content
```bash
git stash apply
```

*  Pop - apply stashed changes to the local repository commiting them. This will delete the stash and its content
```bash
git stash pop
```

#### Cherry Pick
Choose one commit and apply its changes to another branch (and optionally commit them).

![cherry pick](https://scontent.fcia7-1.fna.fbcdn.net/v/t39.30808-6/240051970_4314619585286394_3321527657436640696_n.png?_nc_cat=111&ccb=1-7&_nc_sid=9267fe&_nc_ohc=-k6n_Vt02k8AX9B17K9&_nc_ht=scontent.fcia7-1.fna&oh=00_AT_0W0UUJ99NdNOtbpe-Y5rEGx1wDN8inEIOx4jUozZmcA&oe=634BA13E)


#### Squash Commit
Squash means to collapse two or more consecutive commit into a single commit. Squash will the all changes that took place in the latests commit and apply directly to a parent selected commit.

In order to Squash multiple commits togher they have to respect the following criteria:
* More than one commit selected
* Youngest commit is the HEAD
* Genealogically consecutive
* Chrnologically consecutive 
* Oldest selected commit has parent

>Careful! Squashing rewrite commit history!

#### Git tag
A Git tag is a reference to a specific commit within the history of aGitrepository. In Git, tags are often used to mark releases. To Git tag the most recent commit:

```bash
git tag <tag-name> #tag latest commit on checkout branc
git tag -a <tag-name> [sha-hash-commit] #tag a specific commit
git show <tag-name> 
```

Tag inserted that way will be displayed issuing the command:
```bash
git log --pretty=oneline
```

To push a local tag to a remote:
```bash
git push origin tag-value
git push origin --tags
```

#### Git Hooks
Stored in the .git/hooks (local) they serve as automation toward specific git event trigger.

#### Git Reset

```bash
git reset --soft <commit>
git reset --mixed <commit>
git reset --hard <commit>
```

Reset HEAD to a specific pointer commit. Three modes available:
+ **soft** - undo all the change between HEAD and the commit, but save all the changes as staged
+ **mixed** - undo all the change between HEAD and the commit, but save all the changes as **un**staged
+ **hard** - undo all the change between HEAD and the commit and discard all the changes

#### Git LFS
Git LFS is a Git extension that allow users to save space by storing binary files in a different location (LFS location). Repository may deal for time to time with Audio, Image, Video checking for changes in the binary files. LFS stands for **Large File Storage**. 
+ Avoid slowing down time to clone Remote Repo
+ Pullin down from LFS server only the copy on binary we need

#### Git patch
Old way to share code and contribute to others project.
```bash
git format-patch -1 <commit-SHA> -o <path to save output> #Create a patch. After you should sent it over via email to main repo maintainer
git format-patch <branch-name> -o <path to save output>

git apply <.patch file> #apply patch

```

#### Submodules
It like a child-repository with a repository. Pointers has to be manually update if we want to commit to a parent repo.

#### Worktrees
By default, Git only tracks and moves that pointer along on one branch at a time. The branch you selected with Git checkout is considered your “working tree”.

![[Pasted image 20221012173636.png]]

Just like a tree in the forest, a Git worktree can have several branches at once, which is exactly what the Git worktree command allows you to do.

To keep things organized, Git worktree places each branch into a different specified folder in your file system.  To move between the multiple branches on the worktree, you simply change directory to work against the needed branch.
A Git worktree will always point to a folder on your file system while referencing a particular commit object, like a branch. In order to work in multiple checked out branches at once, you need to add each branch as a new working tree to the Git worktree. There are 4 possible scenarios you can encounter when using Git worktree add:

1.  Add a new working tree to a directory that shares the same name as the branch (the most common method)
2.  Add a new working tree to a directory with a different name as the branch
3.  Create a new Git branch and add a new working tree to a directory that shares the same name as the branch
4.  Create a new branch and add a new working tree to a directory with a different name as the new branch

>No matter what you can only ever have one working tree per directory.

```bash
git worktree add [-f] [--detach] [--checkout] [--lock [--reason <string>]] [-b <new-branch>] <path> [<commit-ish>]

git worktree list [-v | --porcelain [-z]]

git worktree remove [-f] <worktree>

```

More commands [here]([Git - git-worktree Documentation (git-scm.com)](https://git-scm.com/docs/git-worktree))

> Main concept of worktree is to been able to "checkout" multiple branch at the same time.

>Interacting with a single branch would just mean to change a directory, specifically to the directory associated with that branch (created thanks to **_worktree add_**)

