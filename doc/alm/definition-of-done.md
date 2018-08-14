
# Definition of Done (DoD)

Each team has its own Definition of Done (**DoD**) or consistent acceptance criteria across all the issues.

A Definition of Done is a common agreement from the team, defining what should happen to assess an issue that has been completed.

When a board issue is described as **"Done"**, everyone must understand what "Done" means.

## Checklist

Before to close a development user story in the board, commonly all the following items have to happen:

* Code pushed to Git using a feature or bugfix branch. ([Gitflow](../development/branching-model.md))
* Feature is tested against acceptance criteria or if we don't have it, against the description.
* Code builds without warnings on **Travis**. Travis is enabled as a validating stage in the Pull Request workflow.
* Code is unit tested.
* Documentation is updated.
* Code Coverage of new code is higher than **70%** (lines covered)
* Code is peer-reviewed (technical task). Because of the [Git setup](../devops/github-setup.md), pull requests can only be merged after approval.
* After the review, the code is merged to develop branch. ([Gitflow](../development/branching-model.md))
* Security analysis per component/module is performed, when required. (**To be defined**)
* Build is pushed to test server or available to run in a demo environment. (**To be defined**)

## Automation

It's necessary to automate all the previous points as much as possible. This will help us have clean, stable and more secure code.

## Links

* [Collective Code Construction Contract](https://rfc.zeromq.org/spec:42/C4/)
* [Intellij viewing code coverage](https://www.jetbrains.com/help/idea/viewing-code-coverage-results.html)


