# Git Branching Model

This repository follows a branching model inspired by existing models. It maintains two main branches:

- **main**: Reflects a production-ready state.
- **develop**: Reflects the latest delivered development changes for the next release.

## Used Branches

### Main Branch
The `main` branch holds the production-ready code. Every merge into `main` marks a new production release. There are no casual commits on main, only merges comming from the **develop** branch.

### Develop Branch
It's purpose is merging new features for upcoming releases until the branch reaches a stable point and is ready to be released, all of the changes should be merged back into main somehow and then tagged with a release number
- **Origin**: `main`
- **Merge Target**: `main`
- **Naming**: Anything except `main`, `master`, `release-*`, or `hotfix-*`.

### Hotfix Branches
Hotfix branches are used to fix severe bugs that have been released in production and cannot wait for a new release to be fixed. The essence is that work of team members (on the develop branch) can continue, while another person is preparing a quick production fix.
- **Origin**: `main`
- **Merge Target**: `develop` and `main`.
- **Naming**: Ideally something like `hotfix-*`.

### Feature Branches
Feature branches are used to develop new features without specifying the exact release they'll be a part of. These branches exist until the feature is completed, then they're merged into develop to add the new feature to the upcoming release, or discarded if the feature isn't viable.
- **Origin**: `develop`
- **Merge Target**: `develop`
- **Naming**: Anything except `main`, `master`, `develop`, `release-*`, or `hotfix-*`.


## Setting up the repository
In a newly created repository you will already have the main branch, to create the local develop branch and push it to the repository use the following commands.
```sh
    git checkout -b develop main           -> Creates local branch "develop" from branch "main"
    git push --set-upstream origin develop -> Pushes newly created local develop branch to the repository
```

## How to effectively use the branch system
### Develop branch:
#### Creating a develop branch
In a newly created repository you will already have the main branch, to create the develop branch use the following command.
```sh
    git checkout -b develop main
```
#### Merging a stable develop state into main
Once the develop branch is stable, you can merge it into the main branch so that the released version can updated. Releases are tagged for easier identification and to give more information about the release.
```sh
git checkout main         -> Move to "main" branch
git merge --no-ff develop -> Merges "develop" into "main" without fast-forward (keeps historical info of the develop branch)
git tag -a <tagname>      -> Create tag of currect version of main
git push origin <tagname> -> Push tag of currect version of main
git push origin main      -> Push current code to main
```

### Hotfix branch:
#### Creating a hotfix branch
When creating a hotfix because there is a bug on the released version, we want to create it from the released version, main. Change to the main branch and make sure that it's the last version with the following command.
```sh
    git checkout main
    git pull
```

Once done, you can create the hotfix branch via the next command. Make sure to start the name with **hotfix-**.
```sh
git checkout -b hotfix-[name] main
git push --set-upstream origin hotfix-[name]
```

#### Merging a hotfix
After fixing the bug you have to merge it into both main and develop branches, use the following command to update the main branch.
```sh
git checkout main
git merge --no-ff hotfix-[name]
git tag -a <tagname>
git push origin <tagname>
git push origin main
```

### Feature branches
#### Creating a feature branch
When starting to work on a new feature, you have to branch off from the latest version of the develop branch. You can move to the develop branch and update it using the following commands.
```sh
    git checkout develop
    git pull
```

Once you have made sure that it's on the latest version you can create a new feature branch via this command. Update [NAME] to your desired name.
```sh
    git checkout -b [NAME] develop
```

#### Merging a finished feature into develop
Once you have finished a feature, you can merge it into the develop branch so that it can become a new feature to the upcoming release, or discarded if the feature isn't viable. To do that you have to move to the develop branch and merge the feature into it, we will also delete the feature branch while merging it.
```sh
    git checkout develop      -> Move to "develop" branch
    git merge --no-ff [NAME]  -> Merges "[NAME]" into "develop" without fast-forward (keeps historical info of the feature branch)
    git branch -d [NAME]      -> Deletes the branch "[NAME]"
    git push origin develop   -> Pushes the changes on our local branch "develop" to the remote branch "develop" (the one in the repository). Same as git push while on the develop branch.
```



## Command Examples
```


Hotfix Branch
Creating a hotfix branch:

$ git checkout -b hotfix-1.2.1 master
$ ./bump-version.sh 1.2.1
$ git commit -a -m "Bumped version number to 1.2.1"

Finishing a hotfix branch:
sh
Copy code
$ git checkout master
$ git merge --no-ff hotfix-1.2.1
$ git tag -a 1.2.1
$ git checkout develop
$ git merge --no-ff hotfix-1.2.1