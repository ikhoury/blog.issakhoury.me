---
layout: post
title: Streamline Software Development with Git
date: 2020-12-28 09:10:00 +0200
categories: engineering
---

[initial-commit]: /assets/images/git-workflow/initial-commit.png
[first-feature]: /assets/images/git-workflow/first-feature.png
[release]: /assets/images/git-workflow/release.png
[hotfix]: /assets/images/git-workflow/hotfix.png

You are starting to work on a new software project. You want to be able to quickly develop and release new versions of your application while maintaining a well-structured development workflow. We will walk through a software development lifecycle using [Git](https://git-scm.com/) based on the [Trunk Based Development](https://trunkbaseddevelopment.com/) branching strategy. 

The goal of our development team is to have a single source of truth for stable and production ready code, the `master` branch. Every commit to the master branch must be well-tested using an automated continuous integration pipeline. Each one should be considered as a candidate for a production release at any time.

## Initial Commit

![initial commit diagram][initial-commit]

Start by creating a new folder for your project. Next, open a terminal in your project's folder and initialize an empty git repository using `git init`. Then, add a `README.md` file that contains your project's name and a one-line description about it. Stage your changes using `git add README.md` and then create your initial commit using `git commit -m "Initial commit"`.

## First Feature

![first-feature diagram][first-feature]

You plan your first feature with your team and decide to start implementing it. The feature should be small enough to be completed in a short amount of time (at most a couple of days) and meaningful enough to deliver value on its own. In order to iterate quickly and minimize the overhead of coordinating changes across your team, be sure to **only** use _short lived_ feature branches that are regularly (at least daily) updated with the latest changes from master.

You start working on your new feature branch using `git switch -c feature/first-one`. After staging the necceary changes using `git add .` you commit them using `git commit -m "Implement first feature"`. As a suggestion, if your feature can be broken down into smaller sub-tasks that can be developed independently, then break down your branches into `feature/first-one/subtask-1` `feature/first-one/subtask-2` etc...

> **_Branch Name_**: You might already be using an issue tracker such as Jira. Then, you can use your Jira ticket number as the branch name. The tickets will mirror the feature branches in repository.

Here is a hypothetical example of a feature to help better understand this workflow.
In order to implement the new `menu filters` feature we should figure out how to divide it into sub-tasks that can be worked on, integrated, and potentially rolled back independently. We might branch in succession as follows:

- `feature/menu-filters/entity-model`
- `feature/menu-filters/text-search`
- `feature/menu-filters/date-filter`
- `feature/menu-filters/sorting`

The combination of the subtasks completes the `menu filters` user story. Each one can be developed and merged back into master independently so that other collaborators can quickly catch up with our code changes without going through merge hell. In addition, you will receive early feedback from your tests and users.

You submit a pull request for your colleagues to review and approve your changes. There are several strategies that can be used when integrating changes into master. However, to preserve a clean and linear branch history, make sure to always `squash` your commits before merging and avoid creating a second `merge commit`. Usually, your version control software (e.g GitHub, GitLab, Bitbucket...) can reasonably handle this type of merge for you and automatically add the branch name to the title of the commit message. It can also make sure your feature branch is deleted after the changes are merged and the PR is closed.

> **_Note:_** Sync your feature branch frequently with master, at least once a day. Using rebase results in a clean history and is preferable when you are the only one using the branch. However, if your branch is shared and used by others, merge master into your branch. If you choose to rebase please have a good understanding of it.

## Releasing your Software

![release diagram][release]

You decide to release the first version of your software into production. It is important to track the different versions of software that you will eventually release. As a rule of thumb, [semantic versioning](https://semver.org/) is a well known and standardized versioning strategy for software.

Since our single source of truth is the `master` branch, and every commit is production ready code, we will cut our release from master. It's as simple as tagging the commit that represents the release point. You can tag the latest commit using `git tag -a v0.1.0 -m "Release v0.1.0"`.

You just tagged your first production release. Now, we need to bump our version to the next minor one so that all future commits will target the `v0.2.0-SNAPSHOT` version. The `SNAPSHOT` label indicates that the version is currently under development and can be updated with future changes. Once the version is deemed stable, the `SNAPSHOT` label is removed and the version of the software is frozen.

## Patching the Release

![hotfix diagram][hotfix]

What happens if a major bug is discovered in production and the patch cannot wait for the next scheduled release? Your master branch is already  ahead and might contain code that should not be released yet. In the event that we cannot release our patch directly from master, we can create a special `hotfix` branch.

If possible, we first try to patch our code on the `master` branch. This makes sure that the fix is also included in the next production release. We then `cherry pick` our fix onto our new hotfix branch. However, if the fix cannot be directly applied onto master you can merge it onto the new hotfix branch. Make sure that the bug is eventually fixed for the next production release.

We branch out from the last release point into a new `hotfix-0.1.0` branch using `git checkout v0.1.0 && git switch -c hotfix-0.1.0`. Set the version to the next snapshot version `v0.1.1-SNAPSHOT`. Then, commit the fix either from master or your bugfix branch. Finally, create a new release from our hotfix branch with the patch version `v0.1.1`. You can now safely release the new patched version into production, while continuing to work on the next production release with no disruption.

## Cheat Sheets

Step-by-step instructions for each scenario. Each phase assumes you are in an up-to-date master branch.

### Cheat Sheet: Initial Commit

- `mkdir my-project`
- `cd my-project/`
- `git init`
- Create `README.md` file
- `git add README.md`
- `git commit -m "Initial commit"`

### Cheat Sheet: First Feature

- `git switch -c feature/first-one`
- Add new feature
- `git add .`
- `git commit -m "Implement first feature"`
- Create PR for review and merge your changes into master. Squash merge into master using `git merge --squash --no-commit feature/first-one`

### Cheat Sheet: Releasing your Software

- `git tag -a v0.1.0 -m "Release v0.1.0"`
- Bump to next snapshot version
- `git commit -am "Bump to v0.2.0-SNAPSHOT"`

### Cheat Sheet: Patching the Release

- `git checkout v0.1.0`
- `git switch -c hotfix-0.1.0`
- Bump to next snapshot version
- `git commit -am "Bump to v0.1.1-SNAPSHOT"`
- Integrate fix from master or bugfix branch
- `git tag -a v0.1.1 -m "Hotfix v0.1.1"`
- Bump to next snapshot version
- `git commit -am "Bump to v0.1.2-SNAPSHOT"`
