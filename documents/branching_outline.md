# Branching

## Quick Legend

| Instance   | Branch     | Description, Instructions, Notes |
|------------|------------|----------------------------------|
| Production | main       | Accepts merges from Releases and Hotfixes |
| Hotfix     | hotfix-*   | Always branch off Main |
| Release    | release-*  | Always branch off Develop, Accepts merges back into Main and Develop |
| Develop    | develop    | Always branch off Main, Accepts merges from Features and Bugfixes |
| Features   | feature-*  | Always branch off Develop |
| Bugfixes   | bug-*      | Always branch off Develop |

## Main Branches

The main repository will always hold two permanent branches:

* `main`
* `develop`

Consider `origin/main` to always represent the latest code deployed to production. During day to day development, the `main` branch will not be interacted with.

The `develop` branch is where the source code of `HEAD` always reflects a state with the latest delivered development changes for the next release. As a developer, you will be branching and merging from `develop`.

When the source code in the `develop` branch is stable and ready for release, a `release` branch is created. All of the changes in the `release` branch will be merged into `main` and `develop` when the release is complete, and the `release` branch will be deleted.

## Develop Branch

Each developer will branch off from the `develop` branch. When a developer completes a feature, they will create a pull request to merge their feature branch into the `develop` branch. The pull request will be reviewed by the lead and then merged into `develop`

Pull down the latest changes from `main` and merge into `develop`:

```bash
$ git checkout main
$ git pull
$ git checkout develop
$ git merge main
```

## Supporting Branches

Supporting branches are used to aid parallel development between team members, ease tracking of features, and to assist in quickly fixing live production problems. Unlike the main branches, these branches always have a limited life time, since they will be removed eventually.

The different types of branches we may use are:

* Feature branches
* Bugfix branches
* Release branches
* Hotfix branches

Each of these branches have a specific purpose and are bound to strict rules as to which branches may be their originating branch and which branches must be their merge targets. Each branch and its usage is explained below.

### Feature Branches

Feature branches are used when developing a new feature or enhancement which has the potential of a development lifespan longer than a single deployment. When starting development, the deployment in which this feature will be released may not be known. No matter when the feature branch will be finished, it will always be merged back into the `develop` branch.

During the lifespan of the feature development, the lead should periodically merge the `develop` branch into the feature branch to keep it up-to-date.

### Feature Branch Usage

If the branch does not exist yet (check with the Lead), create the branch locally and then push to GitHub. A feature branch should always be available to other users on GitHub. That is, development should never exist in just one developer's local branch.

    
```bash
$ git checkout -b feature-id main                   // creates a local branch for the new feature
$ git push origin feature-id                        // makes the new feature remotely available
```

Periodically, changes made to `develop` (if any) should be merged back into your feature branch.

```bash
$ git merge develop                                 // merges changes from develop into feature 
```

When development on the feature is complete, the lead (or person in charge) should merge changes into `develop` and then make sure the remote branch is deleted.

```bash
$ git checkout develop                              // change to the develop branch  
$ git merge --no-ff feature-id                      // makes sure to create a commit object during merge
$ git push origin develop                           // push merge changes
$ git push origin :feature-id                       // deletes the remote branch
```

### Bug Branches

Bug branches differ from feature branches only semantically. Bug branches will be created when there is a bug on the live site that should be fixed and merged into the next deployment. For that reason, a bug branch typically will not last longer than one deployment cycle. Additionally, bug branches are used to explicitly track the difference between bug development and feature development. No matter when the bug branch will be finished, it will always be merged back into `develop`.

Although likelihood will be less, during the lifespan of the bug development, the lead should watch the `develop` branch (network tool or branch tool in GitHub) to see if there have been commits since the bug was branched. Any and all changes to `develop` should be merged into the bug before merging back to `develop`; this can be done at various times during the project or at the end, but time to handle merge conflicts should be accounted for.

* Must branch from: `develop`
* Must merge back into: `develop`
* Branch naming convention: `bug-<bug-task-id or description>`

#### Working with a bug branch

If the branch does not exist yet (check with the Lead), create the branch locally and then push to GitHub. A bug branch should always be available to other users on GitHub. That is, development should never exist in just one developer's local branch.

```
$ git checkout -b bug-id develop                    // creates a local branch for the new bug
$ git push origin bug-id                            // makes the new bug remotely available
```

Periodically, changes made to `develop` (if any) should be merged back into your bug branch.

```
$ git merge develop                                 // merges changes from master into bug branch
```

When development on the bug is complete, [the Lead] should merge changes into `develop` and then make sure the remote branch is deleted.

```
$ git checkout develop                              // change to the develop branch  
$ git merge --no-ff bug-id                          // makes sure to create a commit object during merge
$ git push origin develop                           // push merge changes
$ git push origin :bug-id                           // deletes the remote branch
```

### Release Branches

Release branches are created when `develop` is ready for a new release. They are branched off from `develop`. When the release is ready, the release branch is merged into `main` and `develop`, and then it is deleted.

* Must branch from: tagged `develop`
* Must merge back into: `main` and `develop`
* Branch naming convention: `release-<release-version-tag or description>`

### Working with the release branch

If the branch does not exist yet (check with the Lead), create the branch locally and then push to GitHub. A release branch should always be available to other users on GitHub. That is, development should never exist in just one developer's local branch.

```bash
$ git checkout -b release-id develop                // creates a local branch for the new release
$ git push origin release-id                        // makes the new release remotely available
```

When development on the release is complete, [the Lead] should merge changes into `main` and develop and then update the tag.

```bash
$ git checkout main                                 // change to the main branch
$ git merge --no-ff release-id                      // forces creation of commit object during merge
$ git tag -a <tag>                                  // tags the release
$ git push origin main --tags                       // push tag changes

$ git checkout develop                              // change to the main branch
$ git merge --no-ff release-id                      // forces creation of commit object during merge
$ git push origin develop --tags                    // push tag changes
```


### Hotfix Branches

A hotfix branch comes from the need to act immediately upon an undesired state of a live production version. Additionally, because of the urgency, a hotfix is not required to be be pushed during a scheduled deployment. Due to these requirements, a hotfix branch is always branched from the `main` branch.

* Must branch from: tagged `main`
* Must merge back into: `main` and `develop`
* Branch naming convention: `hotfix-<hotfix-task-id or description>`

#### Working with a hotfix branch

If the branch does not exist yet (check with the Lead), create the branch locally and then push to GitHub. A hotfix branch should always be available to other users on GitHub. That is, development should never exist in just one developer's local branch.

```
$ git checkout -b hotfix-id main                  // creates a local branch for the new hotfix
$ git push origin hotfix-id                       // makes the new hotfix remotely available
```

When development on the hotfix is complete, [the Lead] should merge changes into `main` and then update the tag.

```
$ git checkout main                                 // change to the main branch
$ git merge --no-ff hotfix-id                       // forces creation of commit object during merge
$ git tag -a <tag>                                  // tags the fix
$ git push origin main --tags                       // push tag changes
```

Merge changes into `develop` and then delete the remote hotfix branch.

```
$ git checkout main                                  // change to the main branch
$ git pull                                           // pull down the latest changes from main            
$ git checkout develop                               // change to the develop branch
$ git merge main                                     // merge changes from main into develop
$ git push origin develop                            // push merge changes
$ git push origin :hotfix-id                         // deletes the remote branch
```