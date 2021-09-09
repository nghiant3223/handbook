# Undoing Changes

---
# Git checkout
### Reviewing old commits
- One of the best utilities for reviewing the history of a Git repository is the git log command (using --oneline option for simplification). 
- Each commit has a unique SHA-1 identifying hash. These IDs are used to travel through the committed timeline and revisit commits.
- By default, git log will only show commits for the currently selected branch.
- You can view all commits across all branches by executing git log --branches=*.
- When you have found a commit reference to the point in history you want to visit, you can utilize the git checkout command to visit that commit.
- During the normal course of development, the HEAD usually points to main or some other local branch, but when you check out a previous commit, HEAD no longer points to a branch—it points directly to a commit.This is called a “detached HEAD” state.
### Viewing an old revision
- View your commit history:
```
git log --oneline
```
- Checkout to an old commit, you can look at files, compile the project, run tests, and even edit files without worrying about losing the current state of the project. Nothing you do in here will be saved in your repository :
```
git checkout a1e8fb5
```
- To continue developing, you need to get back to the “current” state of your project:
```
git checkout main
```
### How to undo a commit with git checkout
- In a detached state, any new commits you make will be orphaned when you change branches back to an established branch. Orphaned commits are up for deletion by Git's garbage collector.
- From the detached HEAD state, we can execute git checkout -b new_branch_without_crazy_commit. This will create a new branch named new_branch_without_crazy_commit and switch to that state.

# Git clean
- git clean command operates on untracked files.
- By default, Git is globally configured to require that git clean be passed a "force" option to initiate.
- Executed git clean is not undo-able.
### Common options and usage
- The -n option will perform a “dry run” of git clean. This will show you which files are going to be removed without actually removing them.
- By default git clean will not operate recursively on directories.
```
$ git clean -n
Would remove untracked_file
```
- The force option initiates the actual deletion of untracked files from the current directory. Force is required unless the clean.requireForce configuration option is set to false. This will not remove untracked folders or files specified by .gitignore.
```
$ git clean -f 
Removing untracked_file
```
- Additionally, a < path > value can be passed with the -f option that will remove a specific file.
```
git clean -f <path>
```
- The -d option tells git clean that you also want to remove any untracked directories, by default it will ignore directories.
```
$ git clean -dn
Would remove untracked_dir/
$ git clean -df
Removing untracked_dir/
```
- The -x option tells git clean to also include any ignored files.
- The below example demonstrates a combination with -f that will remove untracked files from the current directory as well as any files that Git usually ignores.
```
git clean -xf
```
### Summary
- git clean is a convenience method for deleting untracked files in a repo's working directory. Untracked files are those that are in the repo's directory but have not yet been added to the repo's index with git add.
# Git revert
- The git revert command can be considered an 'undo' type command.
- It is not a traditional undo operation. Instead of removing the commit from the project history, it figures out how to invert the changes introduced by the commit and appends a new commit with the resulting inverse content.
- This prevents Git from losing history, which is important for the integrity of your revision history and for reliable collaboration.
- Refs or references are the pointers to commits.
- The HEAD (pointer) always points to the tip of our current branch in the repository.
### How it works
- A revert operation will take the specified commit, inverse the changes from that commit, and create a new "revert commit".
- The ref pointers are then updated to point at the new revert commit making it the tip of the branch.
```
git revert HEAD
```
- Git revert expects a commit ref was passed in and will not execute without one. Here we have passed in the HEAD ref. This will revert the latest commit.
### Common options
```
-e
--edit
```
- This is a default option and doesn't need to be specified. This option will open the configured system editor and prompts you to edit the commit message prior to committing the revert.
```
--no-edit
```
- This is the inverse of the -e option. The revert will not open the editor.
```
-n
--no-commit
```
- Passing this option will prevent git revert from creating a new commit that inverses the target commit. Instead of creating the new commit this option will add the inverse changes to the Staging Index and Working Directory.
# Git reset
