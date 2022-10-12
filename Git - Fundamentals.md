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

### Checkoout
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
git checkout <commit-hash>
```

To retrieve a commit's hash you can issue:
```bash
git log
```

#### Git Detached HEAD

It’s important to be aware: checking out a Git commit will move the HEAD point to a commit instead of a branch. This will put your repository in what’s called **detached HEAD state**.
When you are in detached HEAD state in Git, you are not able to commit changes to a branch.