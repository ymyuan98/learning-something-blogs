> From [here](https://sentry.io/answers/delete-a-git-branch-locally-and-remotely/).

## How to delete a git branch locally and remotely? 

* First, make sure that the repository is not currently on the branch to be deleted. 
That is, use `git checkout another-branch` to switch to another branch not to be deleted: 

```
git checkout main  # This command is to switch to `main` branch.
```

* To delete a **local** branch that **has been fully merged**, use `git branch -d`:

```
git branch -d branch-to-delete
```

* To delete a **local** branch that **has not been fully merged** (i.e., force delete), use `git branch -D`:

```
git branch -D branch-to-delete
```

* To delete a **remote** branch, use `git push --delete`. Assuming that the remote is named `origin`, use the command:

```
git push origin --delete branch-to-delete
```
