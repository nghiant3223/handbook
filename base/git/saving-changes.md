# Saving changes

---
- A commit is the Git equivalent of a "save". Traditional saving should be thought of as a file system operation that is used to overwrite an existing file or write a new file. Alternatively, Git committing is an operation that acts upon a collection of files and directories.
- The stash is an ephemeral storage area for changes that are not ready to be committed. 
- A Git repository can be configured to ignore specific files or directories. This will prevent Git from saving changes to any ignored content.
# Git add
- The git add command adds a change in the working directory to the staging area. It tells Git that you want to include updates to a particular file in the next commit.
### How it works
- The git add and git commit commands compose the fundamental Git workflow. 
- In addition to git add and git commit, a third command git push is essential for a complete collaborative Git workflow. git push is utilized to send the committed changes to remote repositories for collaboration.
### The staging area
- The primary function of the git add command, is to promote pending changes in the working directory, to the git staging area.
- Can think it as a buffer between the working directory and the project history.
- The stage lets you group related changes into highly focused snapshots before actually committing it to the project history
# Git commit
- The git commit command captures a snapshot of the project's currently staged changes.
- Committed snapshots can be thought of as “safe” versions of a project—Git will never change them unless you explicitly ask it to.
### How it works
- At a high-level, Git can be thought of as a timeline management utility. Commits are the core building block units of a Git project timeline.
- Commits can be thought of as snapshots or milestones along the timeline of a Git project. 
- Git Snapshots are always committed to the local repository. 
### Some options
```
git commit --amend
```
- Passing this option will modify the last commit. Instead of creating a new commit, staged changes will be added to the previous commit.
# Git diff
- Diffing is a function that takes two input data sets and outputs the changes between them. git diff is a multi-use Git command that when executed runs a diff function on Git data sources. 
- The git diff command is often used along with git status and git log to analyze the current state of a Git repo.
## Comparing files: git diff file
- The git diff command can be passed an explicit file path option. 
- The below example is scoped to ./path/to/file when invoked, it will compare the specific changes in the working directory, against the index, showing the changes that are not staged yet.
```
git diff HEAD ./path/to/file
```
- Another example, this command will compare the staged changes with the local repository. The --cached option is synonymous with --staged.
```
git diff --cached ./path/to/file
```
### Comparing all changes
- Invoking git diff without a file path will compare changes across the entire repository.
### Changes since last commit
- By default git diff will show you any uncommitted changes since the last commit.
### Comparing files between two different commits
- git diff can be passed Git refs to commits to diff. Some example refs are, HEAD, tags, and branch names. 
- Every commit in Git has a commit ID which you can get when you execute GIT LOG. You can also pass this commit ID to git diff.
```
git log --prety=oneline
957fbc92b123030c389bf8b4b874522bdf2db72c add feature
ce489262a1ee34340440e55a0b99ea6918e19e7a rename some classes
6b539f280d8b0ec4874671bae9c6bed80b788006 refactor some code for feature
646e7863348a427e1ed9163a9a96fa759112f102 add some copy to body
```
### Comparing branches
- Branches are compared like all other ref inputs to git diff.
```
git diff branch1..other-feature-branch
```
### Comparing files from two branches
- To compare a specific file across branches, pass in the path of the file as the third argument to git diff
```
git diff main new_branch ./diff_test.txt
```
# Git stash
- git stash temporarily shelves (or stashes) changes you've made to your working copy so you can work on something else, and then come back and re-apply them later on.
### Stashing your work
- The git stash command takes your uncommitted changes (both staged and unstaged), saves them away for later use, and then reverts them from your working copy.
- Note that the stash is local to your Git repository; stashes are not transferred to the server when you push.
### Re-applying your stashed changes
- You can reapply previously stashed changes with git stash pop.
- Popping your stash removes the changes from your stash and reapplies them to your working copy.
- Alternatively, you can reapply the changes to your working copy and keep them in your stash with git stash apply. This is useful if you want to apply the same stashed changes to multiple branches.
### Stashing untracked or ignored files
- By default, git stash will stash:
  - Changes that have been added to your index (staged changes).
  - Changes made to files that are currently tracked by Git (unstaged changes).
- It will not stash:
  - New files in your working copy that have not yet been staged.
  - Files that have been ignored.
- Adding the -u option tells git stash to also stash your untracked files.
- Adding the -a option includes changes to ignored files as well.
### Managing multiple stashes
- You can run git stash several times to create multiple stashes, and then use git stash list to view them.
- To annotate your stashes with a description, using git stash save "message".
- By default, git stash pop will re-apply the most recently created stash: stash@{0}.
- You can choose which stash to re-apply by passing its identifier as the last argument, for example:
```
git stash pop stash@{2}
```
### Viewing stash diffs
- You can view a summary of a stash with git stash show.
- Or pass the -p option (or --patch) to view the full diff of a stash.
### Partial stashes
- You can also choose to stash just a single file, a collection of files, or individual changes from within files. If you pass the -p option (or --patch) to git stash, it will iterate through each changed "hunk" in your working copy and ask whether you wish to stash it.
### Creating a branch from your stash
- You can use git stash branch to create a new branch to apply your stashed changes to:
```
$ git stash branch add-stylesheet stash@{1}
Switched to a new branch 'add-stylesheet'
On branch add-stylesheet
Changes to be committed:

    new file:   style.css

Changes not staged for commit:

    modified:   index.html

Dropped refs/stash@{1} (32b3aa1d185dfe6d57b3c3cc3b32cbf3e380cc6a)
```
### Cleaning up your stash
- If you decide you no longer need a particular stash, you can delete it with git stash drop:
```
$ git stash drop stash@{1}
Dropped stash@{1} (17e2697fd8251df6163117cb3d58c1f62a5e7cdb)
```
- You can delete all of your stashes with git stash clear.
# .gitignore
- Git sees every file in your working copy as one of three things:
  - tracked - a file which has been previously staged or committed.
  - untracked - a file which has not been staged or committed.
  - ignored - a file which Git has been explicitly told to ignore.
- Ignored files are usually build artifacts and machine generated files that can be derived from your repository source or should otherwise not be committed. Some common examples are:
  - dependency caches, such as the contents of /node_modules or /packages
  - compiled code, such as .o, .pyc, and .class files
  - build output directories, such as /bin, /out, or /target 
  - files generated at runtime, such as .log, .lock, or .tmp
  - hidden system files, such as .DS_Store or Thumbs.db
  - personal IDE config files, such as .idea/workspace.xml