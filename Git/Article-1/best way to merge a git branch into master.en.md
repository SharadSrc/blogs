+++
title = "Best way to merge a git branch into master"
date = 2021-10-02T02:11:12+01:00
draft = false
description = "Git merge is an excellent feature that allows developers to combine different feature branches to the production branch. This tutorial demonstrates various strategies to merge features branches with the master. It will also explore the semantics of merge and rebase as well, so developer choose the best one against the other"
keywords = ["best way to merge a bit branch into master"]
inarticle = true
tags = ["Git", "Git Merge"]
author = "Sharad Dixit"
postlink = 5601931

+++

One of the most powerful features of git is the branch creation and merge operation. Git allows users to create a new branch and merge them into development code. This feature improves the development process workflow for multiple projects by encouraging more specific, smaller, and granular tasks.

In this tutorial article, we will discuss different approaches to merge the git feature branch to master.

The main advantage of git is its branching system. It is on these branches that all the magic of GIT rests! The master branch will carry all the modifications made. Therefore, the goal is not to make the modifications directly on this branch but to make them on other branches, and after various tests, integrate them on the master branch.

In our tutorial for simplicity, let's consider that there are two branches, the `master` branch and the feature branch known as `feature-1`. The main branch is the master branch which contains production code, and the second branch is where modifications will be executed, or new features will be implemented. In the end, if a feature or bug is approved, it will be merged to master.

![Initial_Repository](..\img\Initial_Repository.png)

Let's start the demonstration of the merging of two branches with a real example. To start, we require the following.

## Prerequisites

### Create a Repository on GitHub

An initial repository can be created as introduced in [Github](https://docs.github.com/en/repositories/creating-and-managing-repositories/creating-a-new-repository) 

Next, add two files on the Master branch using the `Add file` button on the repository page. Files' names are as follows.

- `file1.txt`
- `file2.txt`

In this example following text content is added to file1.txt & file 2.txt, respectively.

```bash
$ cat file1.txt
This is dummy text line 1
This is dummy text line 2
$ cat file2.txt
This is dummy test in 2nd file
```

### Clone Repository 

Next, clone your newly created repository from GitHub to your system to create a local copy of the code. Clone URL can be retrieved from the `Code` button as below.

![Clone_Repository](..\img\Clone_Repository.png)

Use the following command to clone.

```bash
$ git clone git@github.com:project/demorepo.git
```

Once cloned successfully, use the following command to display and verify the content of master branch files:

```bash
$ cat file1.txt
This is dummy text line 1
This is dummy text line 2
$ cat file2.txt
This is dummy test in 2nd file
```

### Create Feature Branch

```bash
$ git branch feature-1
```

This command creates a new branch, and it does not create a new commit on git.

### Checkout Feature Branch

Previously, we created a new branch using `git branch feature-1`. But, the active branch is the `master` branch. To activate the new branch, use this command in the terminal:

```bash
$ git checkout feature-1
Switched to branch 'feature-1'
```

The above command will switch the active branch from `master` to `feature-1`. Now, this branch is ready for individual development.

### Modify Files in Feature Branch

We will add some commits or add new lines in the `feature-1` branch. In this case, `file2.txt` will be modified locally and later merged back into the master branch.

For changes till now, our commit diagram will look like below.  Both A and E represent `master` and `feature-1` branch states. Currently, commit `A` and `E` are the same as no files are changed during checkout. 

```bash
A  ← master
   \
     E  ← feature-1
```

Now, `file1.txt` is updated with new text. Use this command to update the content.

```bash
$ echo "file update in feature branch" > file1.txt
```

Now, `file2.txt` has the below content. 

```bash
$ cat file2.txt
This is dummy test in 2nd file
```

The difference between old and new content in `file1.txt` can be verified using the below command.

```bash
$ git diff
diff --git a/file1.txt b/file1.txt
index 11d66d4..48c9378 100644
--- a/file1.txt
+++ b/file1.txt
@@ -1,2 +1 @@
-This is dummy text line 1
-This is dummy text line 2
+file update in feature branch
```

Now, stage this file and create a local commit via the below command.

```bash
$ git add file1.txt
$ git commit -am "update file via feature-1"
[feature-1 22b60b8] update file via feature-1
 1 file changed, 1 insertion(+), 2 d
```

A current snapshot of the commit tree will look like below. Here `F` is a new commit that is created in the previous step.

```bash
A  ← master
  \
    E --- F ← feature-1
```

To demonstrate a real-world example, the remote `master` is also changed simultaneously by other developers, and these changes are pushed as commit `C` and commit `D` to master.

```bash
A --- B --- C --- D ← master
   \
      E --- F ← feature-1
```

Below is the updated content of `file1.txt` in the Github repository at the master branch. Notice line 2 is updated, and lines 3 and 4 are newly created.

![File1_Content](..\img\File1_Content.png)

This can also be validated locally by visualizing your branch history in the command shell in real-time using the below command.

```bash
$ git fetch
$ git log --all --decorate --oneline --graph
```

![Commit_Graph](..\img\Commit_Graph.PNG)

## Preparing to Merge in Git

With Git, we have two possibilities to merge our feature branch changes with the remote `Master` branch: 

1. The `merge` Method
    Git `merge` is a command that commits changes to another branch. It allows developers to take their independent lines of code from the feature branch and integrate them into a single branch on master via the git merge tool.

2. The `rebase` Method
    Git `rebase` is yet another command used for essentially the same purpose, except that it does it very differently.

Let's understand both ways in detail :

## Merge a branch into Master with the `merge` Method in Git

The `merge` aims to consolidate `feature` and `master` branches to the commit that keeps the content of all the involved branches. Git achieves this what is known as a `merge commit`. This also means that `merge` manipulates multiple branches.

When the branches have diverged, i.e. one is not an ancestor of another. Git can achieve the merge by making a new additional commit that has multiple parents. In the following illustration, If you have a commit `D` and a commit `F` in different branches and mix the branches ( via `git merge`) the result is a commit `G` whose parents are `B` and `E`.

```bash
A --- B --- C --- D ---	
  \				         \  
    \					  G  ← master
      E --- F --------- /	
```

In the above diagram, `G` is a newly created commit and created entirely by git. This commit has two parents! and they have an order:

- The first parent is `D`, which was `master` previously.
- The second parent is `F`, which was `feature-1` previously.

This type of commit is called a merge commit.

Now switch back to our repository example and merge the newly created `feature-1` branch to `master`

First, check out the master branch.

```bash
$ git checkout master
```

Now, pull the remote master changes to the local `master`.

```bash
$ git pull origin master

From github.com:repo/demorepo

 * branch            master     -> FETCH_HEAD
   Updating 17cc6b4..a802b6b
   Fast-forward
    file1.txt | 5 ++++-
    1 file changed, 4 insertions(+), 1 deletion(-)
```

Afterward, Use the below command to merge the feature branch i.e `feature-1`, to the currently active branch.

```bash
$ git merge feature-1
```

The `feature-1` branch will be fully merged with the `master` branch if this step is completed successfully. However, if git cannot resolve these merge conflicts automatically, it will fail with merge conflicts error.

This is a very typical scenario; it can happen when two branches modify the same part of the file and git cannot resolve which part to use.  It is exactly what happens with our example. This case is shown below via git.

```bash
Auto-merging file1.txt
CONFLICT (content): Merge conflict in file1.txt
Automatic merge failed; fix conflicts and then commit the result.
```

Whenever git encounters a conflict, it adds `<<<<<<<` & `=======` to highlight the section that caused the conflict and this need to be resolved manually.

![File1_MergeConflict](..\img\File1_MergeConflict.png)

Once it is decided which part to keep in the final master version of the file, an individual has to remove the irrelevant code (including conflict indicators). Eventually, push the changes to the remote branch as below.

```bash
$ git add .
$ git commit -am "resolving the mergeconflict"
[master 1acce69] resolving the mergeconflict
$ git push
```

This way the `feature-1` branch is successfully merged to remote `master`.

Next, we will verify branch history again with the below command.

```bash
git log --all --decorate --oneline --graph
```

![Merge_Graph](..\img\Merge_Graph.png)

We can verify git `merge` has created a `Merge Commit` with commit-id as `1acce69` to merge ` feature-1` branch with `origin/master` .

## Merge a branch into Master with the `rebase` Method

Once again, consider the situation where our feature and master branches are out of sync and need to be merged. Let's also recall the illustration that shows this situation previously. 

```bash
A --- B --- C --- D ← master
   \
      E --- F ← feature-1
```

As an alternative way to merge, you can merge the branch `feature-1` into the branch `master` with rebase option. The `rebase` unifies the branches involved by simply putting commits from the feature branch in front of the master branch.

This will be achieved via the below commands,

```bash
git checkout master
git pull
git checkout feature-1
git rebase master
```

We can have the illustration as below after running rebase.

```bash
A --- B --- C --- D----(operation rebase)----- E--------F   ← master
```

From the illustration above, it is visible that something nice that `rebase` does is produce a linear, cleaner, and easier to read commit history. By merging, it also doesn't generate that additional strange `merge commit`.

The disadvantage of this approach is that rebase changes the entire structure of the branches involved, including rewriting the commit history of these branches. As `rebase` doesn't create a `merge commit`, you don't get the traceability of when two branches merged since rebase generates a linear branch at the end of the process.

## Conclusion

Both commands are very useful; however, in different situations, each side has advantages as below.

### Git `rebase`

- Streamline a complex history.
- Avoid merging commit noise in repositories with busy branches.
- Risky if not used properly as it doesn't preserve history.

### Git `merge`

- Simple to use.
- As an extra merge commit is created every time, it causes commit history to look confusing & dirty.
- Preserves complete history and chronological order.