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
### Git Reset & Three Trees of Git
- Git's internal state management systems: Trees may be a misnomer, as they are not strictly traditional tree data-structures. They are, however, node and pointer-based data structures that Git uses to track a timeline of edits.
- Three trees of Git (see https://www.atlassian.com/git/tutorials/undoing-changes/git-reset for more details):
  - The working directory.
  - Staging index.
  - Commit history.
### How it works
- Where git checkout solely operates on the HEAD ref pointer, git reset will move the HEAD ref pointer and the current branch ref pointer.
### Main Options
#### --hard
- This is the most direct, DANGEROUS, and frequently used option. When passed --hard The Commit History ref pointers are updated to the specified commit.
- This command will reset back to last 2 commits:
```
git reset --hard HEAD~~
```
- The commit before reset can be referenced by the name ORIG_HEAD. In case of resetting by mistake, for example, if reset is in ORIG_HEAD, it is possible to return to the state before reset.
#### --soft
- See https://www.atlassian.com/git/tutorials/undoing-changes/git-reset for more details.
#### --mixed
- See https://www.atlassian.com/git/tutorials/undoing-changes/git-reset for more details.
### Unstaging a file
```
git reset <<path_to_file>>
```
### Removing Local Commits
- The git reset HEAD~2 command moves the current branch backward by two commits, effectively removing the two snapshots we just created from the project history.
- Never perform the above operation if you’ve already pushed your commits to a shared repository.

# HEAD
### Git Detached HEAD
- A detached HEAD occurs when you check out a commit that is not a branch. The term detached HEAD tells you that you are not viewing the HEAD of any repository.
- The HEAD is the most recent version of a branch.
- This is useful if you want to retrieve code you have overwritten in newer commits. You only need to check out the commit you want to view and then you can copy all the code you need from that commit.
### Enter Into a Detached HEAD
```
Enter Into a Detached HEAD
```
- The ~3 tells Git we want to view our repository three commits relative to HEAD. HEAD is the most recent commit in a repository.
- You are in ‘detached HEAD’ state. You can look around, make experimental changes and commit them, and you can discard any commits you make in this state without impacting any branches by performing another checkout.
### Save Changes to a Detached HEAD
- Once you enter into detached HEAD state, you can view and modify the files in a particular commit.
- To save a change from a detached HEAD, you need to create a new Git branch:
```
git branch dev
```
- And you can merge it with the main branch.
### Discard Changes in a Detached HEAD
- Once you are done viewing a previous commit, you can go back to the HEAD of your repository.
- First, we need to discard the changes we made using the Git reset command:
```
git reset --hard
```
- This command will make sure there are no conflicts when we want to move back to our master branch. Next, we can check out the HEAD of our master branch using the checkout command:
```
git checkout master
```
- This will change your current HEAD to the tip of the master branch. None of the changes you made in the detached HEAD state will be reflected on your repository.
### Ref
- [Git Detached HEAD Explanation](https://careerkarma.com/blog/git-head-detached-at/).
  