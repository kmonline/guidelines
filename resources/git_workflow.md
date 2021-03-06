Git Workflow
============

We follow some patterns to organize how to open branches and this is related with our workflow in `KanbanFlow`.

If you didn't read [Pull Requests](https://github.com/kmonline/guidelines/blob/master/resources/pull_requests.md), we recommend to read it first before understanding our Git Workflow.

## Introduction

First, to understand how we organize ourselves, you need to understand types of Swimlanes we have. In KanbanFlow each registered card has a number.

### User Story

For us, an User Story can take up to 5 business days of work. When we pull an User Story to work we have the following steps:

* Preparation
* Planning
* Under development
* Review
* Release

During `Planning` session, we write tasks that compound story. For tasks we also have steps to follow:

* TO DO
* In Dev (WIP)
* Dev Done
* Dev In Test (Deployed/Build generated and being tested by developer)
* In test (Deploy/Build generated and being tested by a tester or other developer)
* Test Done

Card types in this swimlane:

* Task (common task)
* Bug (issues generated by tasks in test phase)
* Unplanned (new tasks introduced during development phase)

### Out of Flow

Out of Flow is designed for small cards that can take up to 2 business days of work.

Card types in this swimlane:

* Task
* Bug (issues non-related with opened (under development) User Story)
* Tech debt
* Spike (study, PoC, analysis, etc)

## Branches

We work with a single stable branch: `master`. Everything that is merged into it, we assume that already passed in `Test phase` properly.

Now that you're familiar with `User Story` and `Out of Flow`, let's understand how each one reflect to our branches and accordingly to our Pull Requests:

### User Story

#### User Story card

First we create a branch using `master` as base with the following pattern:

* `dddd-name-of-story`

Where `dddd` stands for Story Card in Kanban Flow, check some examples of naming User Story branches:

* `897-finish-visit-in-offline-mode`
* `569-edit-expenses`
* `642-clustering-and-noise-reduction`

#### User Story task cards

Then we pull a card to work on in `KanbanFlow`, we ignore its number, then we create a branch with the following pattern using story branch as base:

* `xz-describe-what-will-be-done`

Where `xz` stands for initials of developer, check some examples of naming User Story task branches:

* `wt-fix-check-for-existing-month-on-offline-creator`
* `lh-remove-newline-from-customer-address-in-csvs`

If you developed that change in pair programming, you can use both initials ordered alphabetically:

* `lh-wt-fix-airbrake-to-notify-everyone`

Note that you're able to open new branches and Pull Requests as you consider necessary on working in task. To decide that, always keep in mind how easy would be to review your code, so Pull Request will be your guide.

#### Graph example

![https://github.com/kmonline/guidelines/blob/master/images/user_story_flow.png](https://github.com/kmonline/guidelines/blob/master/images/user_story_flow.png)

### Out of Flow

For this swimlane we pull a card to work on in `KanbanFlow`, considering its number, we'll always have a branch with the following pattern using `master` as base:

* `dddd-description-of-task`

Where `dddd` stands for card in Out of Flow swimlane in KanbanFlow. Check some examples of naming out of flow branches:

* `842-fix-manual-visits-for-same-origin-and-destination`
* `858-zoom-traveled-path-images-on-hover`
* `829-add-origin-address-to-visit-details-page`

#### Graph example

![https://github.com/kmonline/guidelines/blob/master/images/out_of_flow_flow.png](https://github.com/kmonline/guidelines/blob/master/images/out_of_flow_flow.png)

#### One Pull Request vs. One card

In some cases one Pull Request won't be enough to reflect KanbanFlow card, in this case you can create this parent branch, then create child branch from it. Imagine that `829-add-origin-address-to-visit-details-page` requires more than one Pull Request, you can create a branch using it as base:

* `xz-normalize-origin-address-for-visits`

In the branch above, I've identified a step as requirement before develop my task and I can do that for the next one also:

* `xz-add-origin-address-to-visit-details-page`

In example above, we ended up opening two Pull Requests using `829-add-origin-address-to-visit-details-page` as base. Making easier to review our code.

#### Graph example

![https://github.com/kmonline/guidelines/blob/master/images/complex_out_of_flow_flow.png](https://github.com/kmonline/guidelines/blob/master/images/complex_out_of_flow_flow.png)

## Advancing to Test Phase

As we only merge branches into `master` when it has passed in `Test Phase`, will be describe how to advance your change to be tested.

### Platform

TODO

### Mobile

#### Android

TODO

#### iOS

In order to test your iOS change, you should verify if all changes that relies in API has been deployed to QA server. Now you should generate a build using `TestFlight` platform with your task.

### Testing more than one task at once

To test more than one task, enter the `qa` branch.

We use `qa` branch as an ephemeral branch to add many tasks on it, test, then discard it. You should never change your base branch to `qa` as it can be deleted anytime. So, if you have, for instance the following branches to test:

* `xz-fix-button`
* `dddd-introduce-new-config`

To add them to `qa` branch, we will follow these steps:

1. Tell developers that `qa` branch will be reset and ask if someone need to add something to `qa` branch also
2. Delete `qa` branch remotely: `git push origin :qa`
3. Delete `qa` branch locally: `git branch -D qa`
4. Go to `master`: `git checkout master`
5. Ensure your `master` branch is updated: `git pull origin master`
6. Create a brand new `qa`: `git checkout -b qa`
7. Push it: `git push origin qa`
8. Merge your stuff: `git merge xz-fix-button`
9. Push it: `git push origin qa`
10. Merge new stuff: `git merge dddd-introduce-new-config`
11. Push it: `git push origin qa`

If you need to bump version, build number, etc, feel free to do it and commit in `qa` branch. Keep in mind that it will be discarded after Test Phase.

*Note*: If you don't have more than one change to test at once, avoid using `qa` branch.
