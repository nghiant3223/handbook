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
