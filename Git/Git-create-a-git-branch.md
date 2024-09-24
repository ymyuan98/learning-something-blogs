From [here](https://stackoverflow.com/questions/66882952/how-to-create-new-local-branch-and-switch-between-branches-in-git).

## How to create a new local branch and switch between branches in Git

Before the following steps, open a terminal under the local (git) directory. 

* To create and switch to a new branch in Git:
```
git checkout -b new-branch-name
```

> We may use `git checkout branch-name` to switch between branches.

* Verify if you are working on that branch:
```
git branch
```

* If you want to push this new branch to a remote repository, use:
```
git push -u origin new-branch-name
```
 
