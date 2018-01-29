# Contributions guidelines

## Before you start

1. Install Git client
2. Make sure your git client reflects your name and email address as per your GitHub account.

```
git config --global user.name "John Doe"
git config --global user.email johndoe@example.com
```

## Creating a feature or bugfix

1. Fork the repository
2. Make sure your develop branch is fully up to date with the upstream
3. Branch from the head of the develop branch
4. If at all possible, name your branch according to the issue that caused you to create it
5. Make your changes, commit/push
6. If develop moves on, rebase your branch on develop. Do not merge develop to the branch. 

## Submitting a feature or bugfix

1. When ready, rebase your branch in interactive mode and squash all the commits to one commit.
2. Make sure the commit message in the one commit -
   * Is written in the present tense and imperative mood e.g. "Force widget factor to 7" 
   * Don't exceed 50 characters for the first line
   * Leave a blank line
   * Optionally include any other descriptive text you feel qualifies the reason for the change
   * Preferably include reference (not URL) to the issue that caused you to make this change e.g. PNDA-8
3. Push this to your branch and open a PR
4. GitHub will take the title of the commit as the PR name, do not modify this
5. In the description, include any descriptive text you feel qualifies the reason for the change
6. Note validation that has been carried out
7. Do not fill the description with design discussion and so on - describe the code and what it does

**Commit example**

_Force widget factor to 7_

_PNDA-1234: Widget factors other than 7 cause all kinds of problems. Introduced new WidgetFactorFactory to generate sevens._

## Responding to feedback on the PR
 
1. Keep discussion on the PR if possible and record outcome of side discussions if not
2. If the PR has been closed, do not delete or rebase your branch before re-opening the PR
3. Do not close PRs and open new PRs for the same work
4. Ensure the PR commit history and relationship with develop remains sane
   * If develop moves on, rebase branch on develop
   * If rework of existing changes is required it usually makes sense to rebase and squash to one commit
   * If the additional work is purely additive, an additional commit is often all that's necessary

## Changelogs

Given the difficulty of N-way merging on a single location in a single file on GitHub, we've decided to make modifications to the CHANGELOG a Maintainer activity. Therefore, omit these from the PR and we'll make the change after merging.


