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
- **Naming**: Has to start with `hotfix-*`.

### Feature Branches
Feature branches are used to develop new features without specifying the exact release they'll be a part of. These branches exist until the feature is completed, then they're merged into develop to add the new feature to the upcoming release, or discarded if the feature isn't viable.
- **Origin**: `develop`
- **Merge Target**: `develop`
- **Naming**: Anything except `main`, `master`, `develop`, `release-*`, or `hotfix-*`.

## How to effectively use the branch system
### Main branch:
#### Creating a main branch
The main branch is automatically created when the repository is initializated. It's possible that instead of main the branch is called master.

#### Merging main branch into another branch
Merging the main branch into other branches is not recomended, but in case that you need to do it you can follow the next commands. Before running them make sure that you are in the main branch, if you are not, you will be merging a different branch into the one that you wanted to update main
```sh
git checkout [NAME]     -> Move to "[NAME]" branch
git merge --no-ff main  -> Merges "main" into "[NAME]" without fast-forward (keeps historical info of the develop branch)
git push origin [NAME]  -> Pushes the changes from our local branch "[NAME]" to the remote branch "[NAME]" (origin). Equal to git push while on the [NAME] branch.
```

### Develop branch:
#### Creating a develop branch
In a newly created repository you will already have the main branch, to create the develop branch use the following commands.
```sh
    git checkout -b develop main           -> Creates local branch "develop" from branch "main"
    git push --set-upstream origin develop -> Pushes newly created local develop branch to the repository
```
#### Merging a stable develop state into main
Once the develop branch is stable, you can merge it into the main branch so that the released version can updated. Releases are normally tagged for easier identification and to give more information about the release.
```sh
git checkout main
git merge --no-ff develop
git tag -a <tagname>      -> Optional, creates tag of currect version of main. Ex: v1.0
git push origin <tagname> -> Optional, pushes tag of currect version of main
git push origin main
```

### Hotfix branch:
#### Creating a hotfix branch
When creating a hotfix because there is a bug on the released version, we want to create it from the released version, main. Change to the main branch and make sure that it's the last version with the following commands.
```sh
    git checkout main
    git pull
```

Once done, you can create the hotfix branch via the next commands. Make sure to start the name with **hotfix-**.
```sh
git checkout -b hotfix-[NAME] main
git push --set-upstream origin hotfix-[NAME]
```

#### Merging a hotfix
After fixing the bug you have to merge it into both the main and develop branches, use the following commands to update the main branch as well as to delete the hotfix branch. Creating a tag is **mandatory**, you don't want to have the last tag bugged!
```sh
git checkout main
git merge --no-ff hotfix-[NAME]
git push origin --delete hotfix-[NAME]
git tag -a <tagname>
git push origin <tagname>
git push origin main
```

And the following commands to update the develop branch.
```sh
git checkout develop
git merge --no-ff main
git push origin develop
```

### Feature branches:
#### Creating a feature branch
When starting to work on a new feature, you have to branch off from the latest version of the develop branch. You can move to the develop branch and update it using the following commands.
```sh
    git checkout develop
    git pull
```

Once you have made sure that it's on the latest version you can create a new feature branch via this command.
```sh
    git checkout -b [NAME] develop
```

#### Merging a finished feature into develop
Once you have finished a feature, you can merge it into the develop branch so that it can become a new feature to the upcoming release, or discarded if the feature isn't viable. To do that you have to move to the develop branch and merge the feature into it, we will also delete the feature branch while merging it.
```sh
    git checkout develop
    git merge --no-ff [NAME]
    git push origin --delete [NAME]
    git push origin develop
```

## Visualization
You can visualize the branch architecture with the following command.
```sh
git log --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit --date=relative
```
I recommed creating an alias so that you don't have to writte or copy all of that every single time.