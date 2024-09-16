# Git Branching Model

This repository follows a branching model inspired by the existing model [git-flow](https://nvie.com/posts/a-successful-git-branching-model/). 
<br><br>
It maintains two main branches:

- **main**: Reflects a production-ready state.
- **dev**: Reflects the latest delivered development changes for the next release.

As well as multiple, if necessary, secondary branches:
- **hotfix-***: Reflects a severe bug fix that cannot wait for the next release.
- **feature**: Reflects the new features that can be implemeted to the project in next releases.

## Used Branches

### Main Branch
The `main` branch holds the production-ready code. Every merge into `main` marks a new production release. There are no casual commits on `main`, only merges comming from the `dev` and `hotfix-*` branch.

### Dev Branch
It's purpose is merging new features for upcoming releases until the branch reaches a stable point and is ready to be released, all of the changes should be merged back into `main` and then tagged with a release number.
- **Origin**: `main`.
- **Merge Target**: `main`.
- **Naming**: Anything except `main`, `master`, `release-*`, or `hotfix-*`.

### Hotfix Branches
Hotfix branches are used to fix **severe bugs** that have been released in production and **cannot wait** for a new release to be fixed. The essence is that work of team members (on the `dev` branch) can continue, while another person is preparing a quick production fix.
- **Origin**: `main`.
- **Merge Target**: `dev` and `main`.
- **Naming**: Has to start with `hotfix-*`.

### Feature Branches
Feature branches are used to develop new features without specifying the exact release they'll be a part of. These branches exist until the feature is completed, then they're merged into `dev` to add the new feature to the upcoming release, or discarded if the feature isn't viable.
- **Origin**: `dev`.
- **Merge Target**: `dev`.
- **Naming**: Anything except `main`, `master`, `dev`, `release-*`, or `hotfix-*`.

## How to effectively use the branch system
### Main branch:
#### Creating a main branch
The `main` branch is automatically created when the repository is initializated. It's possible that instead of main the branch is called master.

#### Merging main branch into another branch
Merging the main branch into other branches is not recomended, but in case that you need to do it you can follow the next commands. Before running them **make sure that you are in the main branch**, if you are not, you will be merging a different branch into the one that you wanted to update main into.
```sh
git checkout [NAME] &&     -> Move to "[NAME]" branch
git pull &&                -> Pulls remote branch changes
git merge --no-ff main &&  -> Merges "main" into "[NAME]" without fast-forward (keeps historical info of the dev branch)
git push origin [NAME]  -> Pushes the changes from our local branch "[NAME]" to the remote branch "[NAME]" (origin). Equal to git push while on the [NAME] branch.
```
**Fast cmd**:
```sh
git checkout [X] && git pull && git merge --no-ff main && git push origin [X]
```

### Dev branch:
#### Creating a development branch
In a newly created repository you will already have the main branch, to create the development branch use the following commands.
```sh
git pull &&                            -> Pulls remote branch changes
git checkout -b dev main &&        -> Creates local branch "dev" from branch "main"
git push --set-upstream origin dev -> Pushes newly created local dev branch to the repository
```
**Fast cmd**:
```sh
git checkout -b dev main && git pull && git push --set-upstream origin dev
```

#### Merging a stable development state into main
Once the development branch is stable, you can merge it into the main branch so that the released version can be updated. Releases are normally tagged for easier identification and to give more information about the release.
```sh
git checkout main &&
git pull &&
git merge --no-ff dev &&
git tag -a <tagname> &&      -> Optional, creates tag of current version of main. Ex: v1.0
git push origin <tagname> && -> Optional, pushes tag of current version of main
git push origin main
```
**Fast cmd**:
```sh
git checkout main && git pull && git merge --no-ff dev && git push origin main
git checkout main && git pull && git merge --no-ff dev && git tag -a [X] && git push origin [X] && git push origin main
```

### Feature branches:
#### Creating a feature branch
When starting to work on a new feature, you have to branch off from the latest version of the development branch. You can move to the development branch and update it using the following commands.
```sh
git checkout dev &&
git pull
```

Once you have made sure that it's on the latest version you can create a new feature branch via this command.
```sh
git checkout -b [NAME] dev &&
git push --set-upstream origin [NAME]
```

#### Merging a finished feature into dev
Once you have finished a feature, you can merge it into the development branch so that it can become a new feature to the upcoming release, or discarded if the feature isn't viable. To do that you have to move to the development branch and merge the feature into it, we will also delete the feature branch while merging it.
```sh
git checkout dev &&
git pull &&
git merge --no-ff [NAME] &&
git push origin --delete [NAME] &&
git push origin dev
```

### Hotfix branch:
#### Creating a hotfix branch
When creating a hotfix branch, we want to create it from the last released version, `main`. Change to the main branch and make sure that it's the last version with the following commands.
```sh
git checkout main &&
git pull
```

Once done, you can create the hotfix branch via the next commands. Make sure to start the name with `hotfix-`.
```sh
git checkout -b hotfix-[NAME] main &&
git push --set-upstream origin hotfix-[NAME]
```

#### Merging a hotfix
After fixing the bug you have to merge it into both `main` and `dev`, use the following commands to update the main branch as well as to delete the hotfix branch. Creating a tag is **mandatory**, you don't want to have the last tag bugged!
```sh
git checkout main &&
git pull &&
git merge --no-ff hotfix-[NAME] &&
git push origin --delete hotfix-[NAME] &&
git tag -a <tagname> &&
git push origin <tagname> &&
git push origin main
```
**Fast cmd**:
```sh
git checkout main && git pull && git merge --no-ff hotfix-[X] && git push origin --delete hotfix-[X] && git tag -a [X] && git push origin [X] && git push origin main
```

And the following commands to update the development branch.
```sh
git checkout dev &&
git pull &&
git merge --no-ff main &&
git push origin dev
```

## Visualization
You can visualize the branch architecture with the following command.
```sh
git log --graph --abbrev-commit --decorate --date=short --format=format:'%C(bold blue)%h%C(reset) - %C(bold cyan)%ad%C(reset) %C(bold green)(%ar)%C(reset)%C(auto)%d%C(reset)%n'' %C(white)%s%C(reset) %C(dim white)- %an%C(reset)'
```
I recommed creating an alias so that you don't have to writte or copy all of that every single time.
