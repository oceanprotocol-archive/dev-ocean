
Table of Contents
=================

   * [Gitflow Branching Model](#gitflow-branching-model)
      * [Benefits](#benefits)
      * [How it works](#how-it-works)
         * [Develop and Master Branches](#develop-and-master-branches)
         * [Feature Branches](#feature-branches)
         * [Hotfix Branches](#hotfix-branches)
         * [Recommendations](#recommendations)
      * [Overall flow](#overall-flow)
      * [Links](#links)

---

<a href="#gitflow-branching-model"></a>
# Gitflow Branching Model

Gitflow was first published and made popular by [Vincent Driessen at nvie](http://nvie.com/posts/a-successful-git-branching-model/).
Gitflow defines a strict branching model designed around the project release. This provides a robust framework for managing medium size and large projects.

Gitflow is ideally suited for projects that have a scheduled release cycle.
It doesn’t add any new concepts or commands beyond what’s required for the Feature Branch Workflow.
Instead, it assigns very specific roles to different branches and defines how and when they should interact.
In addition to feature branches, it uses individual branches for preparing, maintaining, and recording releases.
Of course, you also get to leverage all the benefits of the Feature Branch Workflow: pull requests, isolated experiments, and more efficient collaboration.

![Gitflow branching model](../img/git-branching-model@2x.png)

<a href="#benefits"></a>
## Benefits

* Well known branching model by the community
* Works pretty well in small and big projects
* Tools adapted/integrated to this model

<a href="#how-it-works"></a>
## How it works


<a href="#develop-and-master-branches"></a>
### Develop and Master Branches

Instead of a single master branch, this Gitflow uses two branches to record the history of the project.
The **master** branch stores the official release history, and the **develop** branch serves as an integration branch for features.
It's also convenient to tag all commits in the master branch with a version number.


<a href="#feature-branches"></a>
### Feature Branches

Each new feature should reside in its own branch, which can be pushed to the central repository for backup/collaboration.
But, instead of branching off of master, feature branches use develop as their parent branch.
When a feature is complete, it gets merged back into develop. Features should never interact directly with master.


<a href="#hotfix-branches"></a>
### Hotfix Branches

Maintenance or “hotfix” branches are used to quickly patch production releases.
Hotfix branches are a lot like release branches and feature branches except they're based on master instead of develop.
This is the only branch that should fork directly off of master. As soon as the fix is complete, it should be merged into both master and develop (or the current release branch), and master should be tagged with an updated version number.

Having a dedicated line of development for bug fixes lets your team address issues without interrupting the rest of the workflow or waiting for the next release cycle. You can think of maintenance branches as ad hoc release branches that work directly with master.


<a href="#recommendations"></a>
### Recommendations

It's recommended to add the ID of the issue on Github to the branch name.
It means, when we need to implement a new feature or fix a bug, this should be registered on Github as an issue.
Each issue has an unique ID, so if we had this information to the branch name, we can track easily where was originated the change.

For example, the branch created to document the branching model is **feature/5-branching_model**, the number **5** relates with [the issue 5 on github](https://github.com/oceanprotocol/engineering/issues/5).


<a href="#overall-flow"></a>
## Overall flow

The overall flow of Gitflow is:

* A develop branch is created from master
* A release branch is created from develop
* Feature branches are created from develop
* When a feature is complete it is merged into the develop branch
* When the release branch is done it is merged into develop and master
* If an issue in master is detected a hotfix branch is created from master
* Once the hotfix is complete it is merged to both develop and master


<a href="#links"></a>
## Links

* https://nvie.com/posts/a-successful-git-branching-model/
* https://datasift.github.io/gitflow/IntroducingGitFlow.html
* https://blog.axosoft.com/gitflow/