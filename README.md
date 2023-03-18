<h1 align="center"> THE CONTINUOUS-RELEASE WORKFLOW</h1>

<p align="center">
  <a href="https://github.com/udohjeremiah/Git-Plus-Hub-Flow">
    <img src="https://img.shields.io/badge/Workflow-Git+Hub%20Flow-F05032?logo=git&logoColor=red" height="50">
  </a>
</p>

This document provides a clear and versatile Git workflow that teams can use to collaborate
on projects. We call this workflow the **Git+Hub Flow**, which combines the
advantages of both the [Github Workflow](https://docs.github.com/en/get-started/quickstart/github-flow)
for continuous deployment and the [Gitflow Workflow](https://nvie.com/posts/a-successful-git-branching-model/)
for release deployment.

Note that continuous deployment is a strategy where features are deployed as soon as they're
ready, while release deployment is a strategy where features are bundled into a release and
then deployed all at once.

If you're unfamiliar with the most commonly used Git workflows, we highly recommend reading
the following articles in the given order. This will help you understand where the
**Git+Hub Flow** fits in and the potential advantages it offers:

- http://scottchacon.com/2011/08/31/github-flow.html
- https://nvie.com/posts/a-successful-git-branching-model/
- https://martinfowler.com/articles/branching-patterns.html

So, what is the **Git+Hub Flow**?

- Anything in the `main` branch is deployable (the **continuous** part).

- The `release-vX.Y` branch is a copy of `main` at a specific point in time
  (the **release** part).

- To work on new features or changes that need to be made to `main` (excluding bug fixes),
  create a branch off of `main` with a descriptive name. This name should typically include
  the name of the team involved at the beginning of the branch name. For example,
  `senior-devs/ui-upgrade`, or `junior-devs/ui-upgrade`. If a single person is making the
  change, they can create a branch with no team name, optionally including their name, such
  as `john/ui-upgrade` or simply `ui-upgrade`.

- Once changes have been merged and pushed to `main`, the feature branch should be deleted,
  and you can deploy immediately if necessary.

- At a periodic interval or based on stated time or how the software is released, create a
  branch from `main` named `release-vX.Y` and tag it as a stable release.

- If a bug is found in `main`, create a branch from it and name it `hotfix-main#<ID>`. After
  fixing the bug, merge `hotfix-main#<ID>` into `main`, and delete the hotfix branch.

- If a bug is found in `release-vX.Y`, create a branch from it and name it
  `bugfix-vX.Y#<ID>`. After fixing the bug, merge `bugfix-vX.Y#<ID>` into both
  `release-vX.Y` and `main`, and delete the bugfix branch.

- In summary, at its core, the central repository will always have two main branches with
  infinite lifetimes: `main` for the unstable deployable release and `release-vX.Y` for the
  stable deployable release. For projects that provide maintenance, fixes, backport, etc.
  for releases other than the current stable release, there can be more than one
  `release-vX.Y` branch.

- When compared to the **stable** `release-vX.Y` branch, the main branch is considered
  **unstable**. This is because new features and changes are frequently added to it without
  prior notice to project users, which could potentially break existing functionality or
  user interactions. In contrast, `release-vX.Y` does not change frequently, and any
  necessary changes will be announced to software users before they are made and/or released
  as a patch to `release-vX.Y`, ensuring stability.

| Branch                    | Protected? | Created from?    | Only merge into?            | Status after merging? |
|:--------------------------|:-----------|:-----------------|:----------------------------|:----------------------|
| `main`                    | YES        | N/A              | `release-vX.Y`              | N/A                   |
|                           |            |                  |                             |                       |
| `release-vX.Y`            | YES        | `main`           | N/A                         | N/A                   |
|                           |            |                  |                             |                       |
| `<teamname><featurename>` | NO         | `main`           | `main`                      | Delete after merging  |
|                           |            |                  |                             |                       |
| `hotfix-main#<ID>`        | NO         | `main`           | `main`                      | Delete after merging  |
|                           |            |                  |                             |                       |
| `bugfix-vX.Y#<ID>`        | NO         | `release-vX.Y`   | `release-vX.Y` and `main`   | Delete after merging  |

### `main`
This is the **unstable** release, which represents the latest state of development. That is,
it serves as the single source of truth, meaning it's the definitive and most up-to-date
version of the code for the project. This branch should always be *deployable*. A pull
request that is reviewed is required to merge code into `main`.

If the project is open source, contributors without write/permission access to the central
repository should fork this branch, which should already be the **default** branch, and
start working from it locally, pushing pull requests to it.

### `release-vX.Y`
This is the **stable** release. A pull request that is reviewed is required to merge code
into `release-vX.Y`. If the branch where code is being merged from is `main`, then the
commit should be tagged as a release.

### `<teamname><featurename>`
If a new feature is assigned to a team, `<teamname>` should be provided. For instance, if
the team `senior-devs` is working on a feature named `ui-upgrade`, they can create their
branch from `main` and name it as `senior-devs/ui-upgrade`.

The `<teamname>` doesn't have to be singular. Multiple sub-teams can exist as long as they
are structured hierarchically. For example, if senior developers delegate work to junior
developers, they can create a branch from `senior-devs/ui-upgrade` and name it as
`junior-devs/senior-devs/ui-upgrade`. Once their part of the work is done, the branch should
be merged into `senior-devs/ui-upgrade` and deleted.

In the same vein, branches such as `seniors/junior-devs/senior-devs/ui-upgrade`,
`juniors/junior-devs/senior-devs/ui-upgrade`, and so on, can be created from
`junior-devs/senior-devs/ui-upgrade`, assuming work is further delegated by `junior-devs` to
seniors and juniors within the `junior-devs` team. Each branch should work in a hierarchical
structure from `senior-devs/ui-upgrade` to the last branch,
`juniors/junior-devs/senior-devs/ui-upgrade`, to ensure the `ui-upgrade` feature is
implemented. All branches should be merged into `senior-devs/ui-upgrade` once all work is
done and then deleted.

If no team is assigned, then there can be no sub-team. Therefore, if a single person is
implementing a feature, `<teamname>` can be omitted. Optionally, their name can be included,
and a branch named after just the feature, such as `john/ui-upgrade` or `ui-upgrade`, can be
created from `main` and worked on. The `<teamname><featurename>` branch should be merged
into the `main` branch once work is completed and deleted afterwards.

### `hotfix-main#<ID>`
This is the branch for any bug fixes against the `main` branch. In a typical environment,
bugs should be opened as issues within the project using a hosting service
(e.g. Github, Gitlab). To work on the bug, a bug fix branch should be created using the
format: `hotfix-main#<ID>`, where `ID` will be the `#issue-number` generated by your hosting
service (e.g., Github, Gitlab, etc.) when the issue was opened. For example,
`hotfix-main#556` is a bug fix for the `main` branch that fixes the bug found by issue
`#556`. The `hotfix-main#<ID>` branch should be merged into the `main` branch and deleted
afterwards.

### `bugfix-vX.Y#<ID>`
This is the branch for any bug fixes against the `release-vX.Y` branch. In a typical
environment, bugs should be opened as issues within the project using a hosting service
(e.g. Github, Gitlab). To work on that bug, a bug fix branch should be created using the
format `bugfix-vX.Y#<ID>`, where `ID` will be the `#issue-number` generated by your hosting
service (e.g., Github, Gitlab, etc.) when the issue was opened.

For example, `bugfix-v1.1#123` is a bug fix for the `release-v1.1` branch that fixes the bug
found by `issue #123`. The `bugfix-vX.Y#<ID>` branch should be merged into both the
`release-vX.Y` and `main` branches and deleted afterwards.

# Workflow Badge
Let contributors know that your project is following the **Git+Hub flow** by adding the
badge to your `README.md`:

```
[![Workflow: Git+Hub](https://img.shields.io/badge/Workflow-Git+Hub%20Flow-F05032?logo=git&logoColor=red)](https://github.com/udohjeremiah/Git-Plus-Hub-Flow)
```
