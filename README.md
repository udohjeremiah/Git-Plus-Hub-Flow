<h1 align="center"> THE CONTINUOUS-RELEASE WORKFLOW</h1>

<p align="center">
  <a href="https://github.com/udohjeremiah/Continuous-Release-Workflow">
    <img src="https://img.shields.io/badge/Git%20Workflow-Continuous--Release-F05032?logo=git&logoColor=red" height="50">
  </a>
</p>

This document provides a clear and versatile Git workflow that teams can use to collaborate
on projects. We call this workflow the **Continuous-Release Workflow**, which combines the
advantages of both the [Github Workflow](https://docs.github.com/en/get-started/quickstart/github-flow)
for continuous deployment and the [Gitflow Workflow](https://nvie.com/posts/a-successful-git-branching-model/)
for release deployment.

Note that continuous deployment is a strategy where features are deployed as soon as they're
ready, while release deployment is a strategy where features are bundled into a release and
then deployed all at once.

If you're unfamiliar with the most commonly used Git workflows, we highly recommend reading
the following articles in the given order. This will help you understand where the
`Continuous-Release Workflow` fits in and the potential advantages it offers:

- http://scottchacon.com/2011/08/31/github-flow.html
- https://nvie.com/posts/a-successful-git-branching-model/
- https://martinfowler.com/articles/branching-patterns.html

So, what is the Continuous-Release Workflow?

- Anything in the `main` branch is deployable (the **continuous** part).

- The `release-vX.Y.Z` branch is a copy of `main` at a specific point in time
  (the **release** part).

- To work on new features or changes to be made to `main` (this involves any changes except
  fixing a bug), create a branch off of `main` with a descriptive name, usually bearing a
  team's name at the beginning. For example, `senior-devs-ui-upgrade`,
  `junior-devs-ui-upgrade` (a branch created from the `senior-devs-ui-upgrade`). If the
  change is being made by a single person, you can simply create a branch with no team name,
  such as `ui-upgrade`.

- Once changes have been merged and pushed to `main`, the feature branch should be deleted,
  and you can deploy immediately if necessary.

- At a periodic interval or based on stated time or how the software is released, create a
  branch from `main` named `release-vX.Y.Z` and tag it as a stable release.

- If a bug is found in `main`, create a branch from it and name it `hotfix-main#<ID>`. After
  fixing the bug, `merge hotfix-main#<ID>` into `main`, and delete the hotfix branch.

- If a bug is found in `release-vX.Y.Z`, create a branch from it and name it
  `bugfix-vX.Y.Z#<ID>`. After fixing the bug, `merge bugfix-vX.Y.Z#<ID>` into both
  `release-vX.Y.Z` and `main`, and delete the bugfix branch.

- In summary, at its core, the central repository will always have two main branches with
  infinite lifetimes: `main` for the unstable deployable release and `release-vX.Y.Z` for
  the stable deployable release.

- Compared to the *stable* `release-vX.Y.Z`, the `main` branch is considered *unstable*
  because it may contain new features and changes can be made to it at any time without
  notice, which could potentially break existing functionality or user interactions. In
  contrast, `release-vX.Y.Z` does not change frequently and any necessary changes will be
  announced to software users before they are made, ensuring stability.

| Branch                    | Protected? | Created from?    | Only merge into?            | Status after merging? |
|:--------------------------|:-----------|:-----------------|:----------------------------|:----------------------|
| `main`                    | YES        | N/A              | `release-vX.Y.Z`            | N/A                   |
|                           |            |                  |                             |                       |
| `release-vX.Y.Z`          | YES        | `main`           | N/A                         | N/A                   |
|                           |            |                  |                             |                       |
| `<teamname><featurename>` | NO         | `main`           | `main`                      | Delete after merging  |
|                           |            |                  |                             |                       |
| `hotfix-main#<ID>`        | NO         | `main`           | `main`                      | Delete after merging  |
|                           |            |                  |                             |                       |
| `bugfix-vX.Y.Z#<ID>`      | NO         | `release-vX.Y.Z` | `release-vX.Y.Z` and `main` | Delete after merging  |

### `main`
This is the **unstable** release, which represents the latest state of development. This
branch should always be *deployable*. A pull request that is reviewed is required to merge
code into `main`.

If the project is open source, then contributors without write/permission access to the
central repository should fork this branch and start working from it locally.

### `release-vX.Y.Z`
This is the **stable** release. A pull request that is reviewed is required to merge code
into `release-vX.Y.Z`. If the branch where code is being merged from is `main`, then the
commit should be tagged as a release.

### `<teamname><featurename>`
If a new feature is assigned to a team, `<teamname>` should be provided. For instance, if
the team `senior-devs` is working on a feature named `ui-upgrade`, they can create their
branch from `main` and name it as `senior-devs-ui-upgrade`.

The `<teamname>` doesn't have to be singular. Multiple sub-teams can exist as long as it is
structured hierarchically. For example, if some work is delegated to junior developers, they
can create a branch from `senior-devs-ui-upgrade` and name it as `junior-devs-ui-upgrade`.
Once their part of the work is done, it is merged into `senior-devs-ui-upgrade`. In the same
vein, branches such as `junior-devs-seniors-ui-upgrade`, `junior-devs-juniors-ui-upgrade`,
and so on, can be created from `junior-devs-ui-upgrade`, with each branch working in a
hierarchical structure from `senior-devs-ui-upgrade` to the last branch,
`junior-devs-juniors-ui-upgrade`, to ensure the `ui-upgrade` feature is implemented. All
branches are merged into `senior-devs-ui-upgrade` once all work is done.

If there is no team assigned, then there can be no sub-team. Thus, if a single person is
implementing a feature, `<teamname>` can be omitted, and a branch named with just the
feature name, such as `ui-upgrade`, can be created from `main` and worked on. The
`<teamname><featurename>` branch should be merged into the `main` branch once work is
completed on it and deleted afterwards.

### `hotfix-main#<ID>`
This is the branch for any bug fixes against the `main` branch. Bugs should be opened as
issues within the project using a hosting service (e.g. Github, Gitlab). To work on the bug,
a bug fix branch should be created using the format: `hotfix-main-vX.Y.Z#<ID>`, where `ID`
will be the `#issue-number` generated by your hosting service (e.g., Github, Gitlab, etc.)
when the issue was opened. For example, `hotfix-main-v1.1.5#556` is a bug fix for the `main`
branch that fixes the bug found by issue `#556`. The `hotfix-vX.Y.Z#<ID>` branch should be
merged into the `main` branch and deleted afterwards.

### `bugfix-vX.Y.Z#<ID>`
This is the branch for any bug fixes against the `release-vX.Y.Z` branch. In a typical
environment, bugs should be opened as issues. To work on that bug, a bug fix branch should
be created using the format `bugfix-vX.Y.Z#<ID>`, where `ID` will be the `#issue-number`
generated by your hosting service (e.g., Github, Gitlab, etc.) when the issue was opened.

For example, `bugfix-v1.1.5#123` is a bug fix for the `release-v1.1.5` branch that fixes the
bug found by `issue #123`. The `bugfix-vX.Y.Z#<ID>` branch should be merged into both the
`release-vX.Y.Z` and `main` branches and deleted afterwards.

# Workflow Badge
Let contributors know that your project is following the Continuous-Release Workflow by
adding the badge to your `README.md`:

```
[![Git Workflow: Continuous-Release](https://img.shields.io/badge/Git%20Workflow-Continuous--Release-F05032?logo=git&logoColor=red)](https://github.com/udohjeremiah/Continuous-Release-Workflow)
```