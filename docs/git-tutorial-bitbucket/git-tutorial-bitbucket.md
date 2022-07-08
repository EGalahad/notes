# Git Tutorial Bitbucket

This page records the notes I take while reading the git tutorial from [bitbucket](https://www.atlassian.com/git/tutorials/learn-git-with-bitbucket-cloud).

- What is it about?

The original tutorial covers most intermediate and advanced topics about git, and is both pratical and concisely illustrative. In its 'How does xxx work?' parts, with carefully constructed examples and real scenario use cases included.

So for those who are interested in learning the mysteries of git without the bother of too much details of underlying implemetations, you should checkout this original tutorial.

As for this note, it is a abridged version of the tutorial, covering most commands mentioned and recording my understanding.

- Why should I bother?

If you are a beginner to git, I may discourage you to read any other git tutorials instead.

When I read this tutorial, I have first learned git one year ago, and used it from time to time mainly to finish my homework assignments. So my bare knowledge of `git add/commit/push` is sufficient for my needs. But I am 'morbidly' addicted to perfectionism, and could not get away with a messy commit history, so this tutorial come in handy as it explains many aspects of restructuring and rewriting commit history for pratical uses.

If you got stuck with git in your workflow, or maybe you are just curious about the mysteries of git, I am sure this tutorial will get you up and going.

## Source Code Management

best practices:

1. commit often: don't worry about repo being messed up, you can combine a group of commits into a single one using a rebase (or using reset).

2. make detailed notes: "why" and "what".

3. review changes before committing: though 1, it is recommended to utilize the staging area as a buffer to help refine the cotents of this commit.

4. use branches.

5. agree on a workflow: this needs to be taken into consideration when working with a team.

## Saving Changes

### `git add`

The primary function of the `git add` command, is to promote pending changes in the working directory, to the git staging area.

The staging area is considered one of the "three trees" of Git, along with, the working directory, and the commit history.

> from the website:
> 
> Instead of committing all the changes you've made since the last commit, you can **group related changes** into several groups. This means you can make all sorts of edits to unrelated files, then go back and split them up into logical commits by adding related changes to the stage and commit them piece-by-piece.

> command tips:
> `git add -p` bring up an interactive staging session for you to choose portions of a file to add to the next commit. This will present you with a chunk of changes and prompt you for a command. Use `y` to stage the chunk, `n` to ignore the chunk, `s` to split it into smaller chunks, `e` to manually edit the chunk, and `q` to exit.

### `git commit`

Same as the staging area, the local repo can be viewed as a buffer between remote repo and local commits. The design of git makes it possible to commit locally and clean up local history before publishing it to the central repo.

Always `git status` before commit.

> from the website:
> This has many advantages over SVN-style collaboration: it makes it easier to split up a feature into atomic commits, keep related commits grouped together, and clean up local history before publishing it to the central repository. It also lets developers work in an isolated environment, deferring integration until they’re at a convenient point to merge with other users. While isolation and deferred integration are individually beneficial, it is in a team's best interest to integrate frequently and in small units.

#### How to write good commit messages?

1. Summarize the entire commit on the first line using less than 50 characters.

2. After a blank line, a detailed explanation listing what have been done.

3. Use present tense. This makes it read more like actions on he repo.

### `git diff`

`git diff`: compare HEAD commit with working directory.

To compare with staging index: `--staged` or `--cached`.

To compare with a specific commit: you can use any ref pointers.

#### Reading diffs: outputs

```git
diff --git a/diff_test.txt b/diff_test.txt
index 6b0c6cf..b37e70a 100644
--- a/diff_test.txt
+++ b/diff_test.txt
@@ -1 +1 @@
-this is a git diff test example
+this is a diff example
```

a typical output is composed of

1. comparison input: command and files;

2. meta data; 

3. markers for changes: identifiers for following chunks;

4. diff chunks: '-' comes from the file marked by `---`. Each chunk has a chunk header inclosed with `@@` symbols, summarizing the chunk diff in the form of `-line,num_extracted+line,num_added`.

> command tips:
> use `git diff --color-words` to highligt words that changed instead of the whole line.

#### Diffing binary files

Git does have a feature that allows you to specify a shell command to transform the content of your binary files into text prior to performing the diff. 

Check the website for detailed setup.

#### dot operators

`git diff branch1..branch2`: same as putting a space.

`git diff branch1...branch2`: the first comparator is set to the common ancestor of two branches.

### `git stash`

#### Basic commands

`git stash`: stash the working directory into a stash commit, and reverts the working directory as well as the staging area to match the HEAD commit.

To include ubtracked files: `-u`.

To include ignored files: `-a`.

To start an interactive session: `-p`.

`git stash list`: list all stashes.

`git stash show`: inspect (diff) the stash.

To see full diff: `-p` or `--patch`.

`git stash pop` (or `apply`): apply the stash to the working directory and staging area, then drop (or keep) the stash.

`git stash clear`: drop all stashes.

#### How git stash works?

Stashes are actually encoded in your repository as commit objects. Depending on what you stashed, a single git stash operation creates either two or three new commits. 

[how git stash encode worktree and index as commits](https://www.atlassian.com/git/tutorials/saving-changes/git-stash#partial-stashes:~:text=.-,How,encodes%20your%20worktree%20and%20index%20as%20commits%3A,-Before%20stashing%2C%20your)

### `.gitignore`

#### Personal and Global (local) Ignore Rules

As `.gitignore` files are pushed to the remote repo, you should not let customed ignore rules be shared with teammates and mess up the whole thing.

define personal ignore patterns for a particular repository in a special file at **`.git/info/exclude`.** These are not versioned, and not distributed with your repository.

define global Git ignore patterns for all repositories on your local system:

Create a `.gitignore` add home folder and set `git config --global core.excludesFile ~/.gitignore`.

#### Ignoring a previously commited file

```bash
echo "debug.log" >> .gitignore
git rm --cached debug.log
git add .gitignore
git commit -m  "start ignoring debug.log"
```

use `--cached` option with `git rm` to delete from the repo (untrack this file in future commits).

omit this option if you want to delete the file from both the repository and your local file system.

#### Committing an ignored file

use `git add` with option `-f` or `--force`.

A better solution is to define an exception to a general rule.

```bash
echo !debug.log >> .gitignore
```

#### Debuging your ignore rules

```bash
git check-ignore -v <file-name>
```

to see which rules ignores this file

the output shows

```bash
<file containing the pattern> : <line number of the pattern> : <pattern>    <file name>
```

## Inspecting a repository

### `git log`

To view all branches: `--branches=*`.

To restrict commit range: `git log <since>..<until>`: both arguments can be either a commit ID, a branch name, HEAD, or any other kind of [revision reference](http://www.kernel.org/pub/software/scm/git/docs/gitrevisions.html).

To restrict to a file: `git log <file>`. This is an easy way to see the history of a particular file.

### `git tag`

`git tag`: list tags.

`git tag v1.1`: create a lightweight tag on HEAD commit.

`git tag -a v1.1 -m "<mesg>"`: create a tag object on HEAD.

see [this](https://stackoverflow.com/questions/1457103/how-is-a-tag-different-from-a-branch-in-git-which-should-i-use-here) for a concise comparison between lightweight tags, tag objects and branches.

### `git blame`

`git blame <filename>`: show line by line the last modification time author and commit message.

To see the whole history for the change of one line, use `git log -S "<string>"` to show commit related to that line.

## Undoing Changes

### `git clean`

`rm`s untracked files in working directory.

Need to be invoked with `-f` option. It is recommended to use `-n` which will output which files will be deleted before actually deleting the files.

To include ignored files: `-x`

To include directories to clean recursively: `-d`

### `git revert`

Can only operate on commit level.

Takes a specified commit, inverse the changes introduced by that commit, and create a new "revert commit" after HEAD.

To only inverse the changes in the working directory and staging index: `-n` or `--no-commit`.

### `git reset`

this part references [git-scm](https://git-scm.com/book/en/v2/Git-Tools-Reset-Demystified)

Can operate on commit level or file level.

#### Commit level

It overwrites the three trees in a specific order

1. Move the branch HEAD points to (stop here if `--soft`).

2. Make the index look like HEAD (stop here if `--mixed`).

3. Make the working directory look like the index. (stops here if `--hard`).

#### File level

`git reset <commit> <filename>`: update `<filename>` in the index to match the version in `<commit>`. If no commit specified, HEAD is used (this is unstaging a file).

#### Use Case: Squashing

You can combine several commits into one that seem more atomic.

Then you can change your HEAD pointer to the commit before the series of commits you want to squash with `--soft` option. Note this leaves your staging index and working tree untouched. Then you can just `commit` the staging index to recover the latest commit.

#### Difference Between `checkout` and `reset`

##### Commit level

`checkout <branch>` and `reset --hard <branch>` are similar in that they both change the working tree and the staging index to match that commit, but checkout is more safe (will make sure working tree is clean) and smart (can merge and update the files in working tree).

##### File level

`checkout <branch> <filename>` change the file in the working tree while `reset <branch> <filename>` only updates the staging index.

## `git rm`

`git rm <filename>`: remove a file from the staging index and the working directory. 

It checks the file is not modified from the one in commit, preventing unintended loss of work in progress. The file removal is not persisted to the repository history until a new commit is created.

To override the safety check: `-f` or `--force`

To see what file will be removed: `-n` or `--dry-run`

To only remove from index: `--cached`

## Rewriting History

### `git commit --amend`

Change the most recent commit.

**Don't amend pushed commits**, this will polute other developers' repo when they pull.

### `git rebase`

`git rebase <new_branch_as_base>` apply the changes on current branch to the specified base branch.

#### interactive mode

To open interactive mode: `-i` or `--interactive`

This will open up a editor, where you can type commands to tune the rebasing process. You can change the order to apply these commits, squash some commits with previous ones or let rebase stop at some commit to `rebase --continue` after you  `commit --amend` to it.

### `git reflog`

Whether you amend to a commit or rebase a series of commits to change your commit history, git makes completely new commits. However, those old commits are still there in the `.git` folder. They are just 'invisible' because no reference (branch, tag) points to them. They are pruned on a regular basis (by default 90 days). This means you can effectively undo any operation that rewrites commit history.

The information about these 'invisible' commits can be derived from `git reflog` command.

> Reference logs, or "reflogs" are a mechanism Git uses to record updates applied to tips of branches and other commit references. Reflog allows you to go back to commits even though they are not referenced by any branch or tag.

`git reflog show <ref>`: show history of `<ref>` changes.

To show relative data information: `--relative-date`. 

`git reflog expire`: prune unreachable reflog entries.

To show which will be pruned: `-n` or `--dry-run`.

`git reflog delete`: delete a reflog entry.

## Syncing

### `git fetch` and `git pull`

`git fetch` only 'fetch'es the branches and commits from the remote without and modification of the local commit history. The fetched branches are named `<remote_repo_name>/<remote_branch_name>`.

`git pull` does one more step further, updating the local branch ref pointers to match that of the remote repos. Seen it as the counterpart of `git push`, you know there will be merge conflicts if there are conflicted changes.

From the Git documentation for [`git pull`](http://git-scm.com/docs/git-pull):

> In its default mode, `git pull` is shorthand for `git fetch` followed by `git merge FETCH_HEAD`.

### Merging

Keep in mind that a branch is just a pointer to a specific commit, which keeps all it ancesters reachable in the commit history (if no reflog is used). It can be seen as the tip of one workflow.

`git merge <src_branch>`: run after `checkout` to the target branch (the branch to merge your modifications on).

#### Two type of merge

1. fast forward: only move ref pointer, no commit created.

2. three-way merge: combine changes made between the source branch and their common ancestor into a commit after the destination branch.

#### Merge conflicts

The conficts resolving process uses the familiar edit / stage / commit workflow to resolve merge conflicts. When merge conflicts arises, use `git status` to show which files need to be resolved.

## Git Workflows

These are just some guidelines on how you use git to facilitate team collaboration rather than concrete rules to be observed. They show what is possible with git, so you can mix and match aspects from different workflows to suit the needs of your team.

### Feature branch workflow

Fork out 'feature' branches from the main branch, and use pull request to notify team members before merging into main branch.

### Gitflow workflow

Aside from the main branch dedicated to record the commit history of releases, another long living branch, called 'develop' in maintained. It is the "main" branch that you work on, while only keeping an abridged version off entire commit history in the main branch for release.

- feature branch: Forked from and merged back to the develop branch.

- release branch: Forked from develop branch when a release is prepared, do some release related work in this branch and merge into both main and develop branch.

- hotfix branch: the only branch that directly diverges from main, merged into main and develop.

## Resetting, Checking Out and Reverting

### Reference

| Command        | Scope        | Common use cases                                                    |
| -------------- | ------------ | ------------------------------------------------------------------- |
| `git reset`    | Commit-level | Discard commits in a private branch or throw away uncommited change |
| `git reset`    | File-level   | Unstage a file                                                      |
| `git checkout` | Commit-level | Switch between branches or inspect old snapshots                    |
| `git checkout` | File-level   | Discard changes in the working directory                            |
| `git revert`   | Commit-level | Undo commits in a public branch                                     |
| `git revert`   | File-level   | (N/A)                                                               |
