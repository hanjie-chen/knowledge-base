---
Title: Git Command Quick Reference
SourceBlob: 4dc645d6cadb799a3f22bf287e2d4c92a225cb15
---

```
BriefIntroduction: Common Git commands, typical scenarios, and common pitfalls organized by workflow
```

<!-- split -->

After creating a repository on GitHub, GitHub will show you what to do next. Follow the repository’s instructions to initialize the local repository and complete the first push.

<img src="./resources/images/new-repo.png" alt="new repo" style="zoom:50%;" />

# Local Workflow

## git status

Displays the current state of the repository.

## git add

I usually go directly to the project root directory and run `git add .` to include everything. This command adds modified files to the staging area.

## git commit

```shell
git commit -m "add your specification of this commit"
```

Each commit records who made which changes and when, allowing you to return to that state later.

### Commit ID

- After each successful commit, Git generates a unique 40-character hexadecimal string called a SHA-1 hash, which serves as the commit ID.
- Even changing a single space will produce a completely different SHA ID.
- When running commands or performing a DevOps rollback, the first seven characters of the SHA, such as `a1b2c3d`, are usually enough to identify the commit precisely.

View the commit history and its corresponding SHA IDs:

``` shell
git log --oneline
```

If you have forgotten the details of the most recent commit, use:

```shell
git show
```

By default, this command displays the contents of `HEAD`, which is the latest commit on the current branch. You can also append a specific SHA ID to inspect an earlier commit: `git show <SHA>`

### git add + git commit

For tracked files: If you have only modified tracked files and do not need to add any new files, you can commit the changes directly with `git commit -a -m "message"`. This command automatically commits modifications to all tracked files, so you do not need to run `git add` first.

For new files: You must add them to the staging area with `git add`, because Git tracks only files that have already been added to version control. New files must first be added with `git add` before they become tracked. In this case, you can use:

```bash
git add . && git commit -m "message"
```

# Remote Sync

## git push

Push local content to GitHub to synchronize it.

### First Push of a New Branch

If you have created a new local branch that does not yet exist in the remote repository, use the `-u` option the first time you push it:

```shell
git push -u origin <branch-name>
```

This command performs two tasks:

1. Creates the remote branch: It creates a new branch with the same name in the remote repository, such as GitHub, and uploads the code.
2. Establishes an upstream relationship: It links the local branch to the remote branch.

Without the `-u` option—using only `git push origin <branch-name>`—the branch will still be created remotely, but no default upstream relationship will be established. This means you will not be able to use the shorter `git push` command later. Git will report an error because it does not know which remote branch should receive the local branch.

### Push Rejected

Sometimes, running `git push` produces an error like this:

```shell
➜ git push
To github.com:hanjie-chen/Test-Website.git
 ! [rejected]        backend-development -> backend-development (fetch first)
error: failed to push some refs to 'github.com:hanjie-chen/Test-Website.git'
hint: Updates were rejected because the remote contains work that you do not
hint: have locally. This is usually caused by another repository pushing to
hint: the same ref. If you want to integrate the remote changes, use
hint: 'git pull' before pushing again.
hint: See the 'Note about fast-forwards' in 'git push --help' for details.
```

This means the remote repository contains commits that do not yet exist locally. You must first run `git pull` to synchronize the latest changes.

## git pull

As the counterpart to `git push`, `git pull` synchronizes the latest changes from a remote repository, such as GitHub, to the local repository.

Suppose we develop the same project on multiple machines—an Azure VM, a local PC, and a MacBook Pro—and run `git push` on one of them. The other machines then need to run `git pull` so their local code catches up with the latest remote state.

By default, running `git pull` on a branch updates only the current local branch. It does not automatically update other local branches, although it will usually refresh information about remote-tracking branches.

### How git pull Works

More precisely:

git pull = git fetch + git merge

- git fetch: Retrieves the latest commits from the current remote branch.
- git merge: Attempts to merge those commits into the current local branch.

Therefore, the current local branch is actually updated. Other local branches do not automatically move forward, although information about remote-tracking branches is usually refreshed.

### Divergent Branches

Suppose the latest code in the remote repository on GitHub has been modified, while the local code has also been modified and committed with `git add` and `git commit`.

Even if the changes do not conflict, running `git pull` locally may produce the following message:

```shell
$ git pull
remote: Enumerating objects: 20, done.
remote: Counting objects: 100% (20/20), done.
remote: Compressing objects: 100% (13/13), done.
remote: Total 13 (delta 7), reused 0 (delta 0), pack-reused 0 (from 0)
Unpacking objects: 100% (13/13), 4.71 KiB | 344.00 KiB/s, done.
From github.com:hanjie-chen/website
   0b18d4f..55d30e5  main       -> origin/main
 * [new tag]         v0.1.0     -> v0.1.0
hint: You have divergent branches and need to specify how to reconcile them.
hint: You can do so by running one of the following commands sometime before
hint: your next pull:
hint:
hint:   git config pull.rebase false  # merge
hint:   git config pull.rebase true   # rebase
hint:   git config pull.ff only       # fast-forward only
hint:
hint: You can replace "git config" with "git config --global" to set a default
hint: preference for all repositories. You can also pass --rebase, --no-rebase,
hint: or --ff-only on the command line to override the configured default per
hint: invocation.
fatal: Need to specify how to reconcile divergent branches.
```

Git is asking for your preference: because both the remote repository on GitHub and the local repository contain different new commits—known as divergent branches—how would you like Git to combine them?

You can use rebase to move your local commits so that they come after the remote commits.

Run the following command in your repository directory:

```shell
$ git pull --rebase
Successfully rebased and updated refs/heads/main.
```

This performs two operations: it first retrieves the remote updates, then **replays your local commits one by one after** the latest commit on `origin/main`. If there are no conflicts, the operation will complete successfully.

Afterward, run `git push`.

# Git State Flow

To understand what the preceding commands do, first think of Git as several distinct state areas.

```text
Working Directory     Staging Area       Local Repository    Remote Repository
       |                    |                   |                   |
       +----- git add ----->+                   |                   |
                            +--- git commit --->+                   |
                                                +---- git push ---->+
```

- `Working Directory`: Where you edit files.
- `Staging Area`: The area containing changes prepared for a commit after running `git add`.
- `Local Repository`: The content stored in the local repository after running `git commit`.
- `Remote Repository`: The content stored remotely after running `git push`.

From this perspective, `git add`, `git commit`, and `git push` each move content forward by one stage, while `git pull` synchronizes the latest state of the remote repository back to the local repository.

# Branch Management

## List Branches

View local branches:

```shell
➜ git branch
* main
```

View remote branches:

```shell
➜ git branch -r
  origin/HEAD -> origin/main
  origin/backend-development
  origin/main
```

View all local and remote branches:

```shell
➜ git branch -a
* main
  remotes/origin/HEAD -> origin/main
  remotes/origin/backend-development
  remotes/origin/main
```

> [!note]
>
> After `git clone`, Git brings information about the remote repository’s branches into the local repository, but by default it checks out only the remote repository’s default branch, usually `main`.
>
> Therefore, `git branch` will usually show only the `main` branch, while `git branch -r` will show the remote branches.

## Switch Branch

The `git checkout <branch-name>` command first looks for a local branch named `branch-name`. If it finds one, it switches to that branch.

If it does not find a local branch, it looks for a remote branch named `branch-name`. If it finds one, it creates a local branch with the same name, establishes a tracking relationship, and then switches to the new local branch.

For example:

```shell
➜ git branch
* main
➜ git branch -r
  origin/HEAD -> origin/main
  origin/backend-development
  origin/main
➜ git checkout backend-development
Switched to a new branch 'backend-development'
branch 'backend-development' set up to track 'origin/backend-development'.
```

## Create Branch

The `git checkout -b <branch-name>` command creates a new branch and immediately switches to it. If the branch already exists, Git reports an error.

This command is shorthand for `git branch <branch-name>` followed by `git checkout <branch-name>`.

The new branch is based on the branch that is currently checked out. For example, if you are currently on the `main` branch, the new `branch-name` branch will be created from `main`.

### First Push to Remote

After creating a branch, it exists only locally. Because the corresponding remote branch does not yet exist, use the following command for the first push:

```shell
git push -u origin <branch-name>
```

If specifying the branch name every time feels cumbersome, you can configure Git’s push behavior:

```bash
git config --global push.default current
```

After this setting is applied, running `git push` automatically pushes the current branch to a remote branch with the same name, creating it if it does not already exist.

## Parallel Branch Work

Suppose we encounter the following situation:

We are developing on the `main` branch while interacting with a coding agent such as Codex or Claude Code. The agent may run for a long time, so we do not need to watch it continuously.

Meanwhile, we want to inspect or modify something on another branch named `k8s-lab`. What should we do?

If both sessions share the same Git working directory, running the following command directly in the current directory:

```shell
git checkout k8s-lab
```

will switch the files in that directory to the state of the `k8s-lab` branch. As a result, the working directory used by the agent session running on `main` will also change, potentially disrupting its context or execution environment.

> [!note]
>
> This also explains why tmux cannot truly solve this problem.
>
> tmux merely provides multiple terminal sessions. If those terminals all operate on the same Git directory, running `git checkout` in any one of them changes the directory state seen by all the others because they share the same working directory.

This is where the `git worktree` command is useful.

It creates another independent working directory for the same Git repository and usually checks out a particular branch in that directory.

For example:

```text
repo
├── website/          -> main
└── website-k8s/      -> k8s-lab
```

In this setup:

- The `website/` worktree currently has `main` checked out.
- The `website-k8s/` worktree currently has `k8s-lab` checked out.

A coding agent session can therefore continue running on the `main` branch, while we enter the other worktree to inspect or modify code on the `k8s-lab` branch without affecting the agent.

You can think of a worktree as expanding a branch into an independent working directory.

In the earlier Git State Flow, the Working Directory represents the location where files are edited. `git worktree` allows the same repository to have multiple independent working directories at the same time.

> [!note]
>
> By default, the same branch cannot be checked out in two worktrees at the same time.

Usage:

Create a new worktree:

```shell
git worktree add ../website-k8s k8s-lab
```

- Creates a new directory at `../website-k8s`.
- Checks out `k8s-lab` in that directory.

In other words: `git worktree add <path> <branch>`.

View the list of worktrees:

```shell
git worktree list
```

Remove a worktree:

```shell
git worktree remove ../website-k8s
```

If the worktree still contains uncommitted changes, Git may refuse to remove it by default.

## Merge Branch

After developing on a branch and reaching a suitable point—for example, completing a feature or finishing a development stage—you can merge the work from that branch into `main`.

Follow these steps.

First, switch to the `main` branch:

```bash
git checkout main
```

Merge the branch into `main`:

```bash
git merge <branch-name> -m "merge message"
```

Push the updated `main` branch to the remote repository, if one exists:

```bash
git push origin main
```

## Delete Branch

After development on a branch is complete and the branch has been merged into `main`, you can delete it to keep the repository tidy.

First, delete the local branch.

### Delete Local Branch

Use the lowercase `-d` option to safely delete a branch that has already been merged into the current branch:

```bash
git branch -d <branch-name>
```

For example: `git branch -d backend-development`

If the branch has not been merged, Git will display a warning and prevent its deletion.

To force-delete a local branch:

If you are certain that you want to delete an unmerged branch, use the uppercase `-D` option:

```bash
git branch -D <branch-name>
```

> [!note]
>
> Running `git branch -d <branch-name>` deletes only the local branch. It has no effect on the branch in the remote repository, such as GitHub.

Next, delete the remote branch.

### Delete Remote Branch

Use the following command:

```bash
git push origin --delete <branch-name>
```

# Rollback

## git restore

Suppose you have modified a file but have not run `git add`, for example:

```bash
$ git status
On branch main
Your branch is up to date with 'origin/main'.

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   compose.yml

no changes added to commit (use "git add" and/or "git commit -a")
```

In this situation, discarding the changes is straightforward. Use the command shown in Git’s message:

```bash
git restore compose.yml
```

Keep the following points in mind:

1. This operation immediately discards all changes made to `compose.yml`.
2. This operation cannot be undone, so confirm that you truly want to abandon the changes before running it.

If you want to inspect the changes before discarding them, use:

```bash
git diff compose.yml
```

This displays the exact changes so that you can decide whether to discard them.

If the changes have already entered the staging area, `git restore <file>` is not sufficient. You will need another method to handle the staged changes.

## git reset

`git reset` moves the current state back to a specified point.

### Reset to the Last Commit

If you only want to discard local changes that have not yet been committed, use:

```bash
git reset --hard HEAD
```

Here:

- `HEAD` points to the latest commit on the current branch.
- `--hard` resets both the Working Directory and the Staging Area.

This command therefore clears changes from the working directory and staging area, but it does not move the local branch back to an earlier commit.

### Reset to the Remote Branch

If you want to discard all local changes and local commits, keeping only the latest state of the remote repository, use:

```bash
git fetch origin
git reset --hard origin/main
```

Here:

- `git fetch origin` retrieves the latest state of the remote repository without automatically merging it.
- `origin/main` points to the latest position of the remote repository’s `main` branch.
- `git reset --hard origin/main` forcibly resets the current branch to the state of the remote branch.

This clears:

- Changes in the Working Directory.
- Changes in the Staging Area.
- Unpushed local commits in the Local Repository.

### Rollback Scope

```text
Working Directory 	  Staging Area 	 local repository     remote repository
   (edit file) ------> (git add) ----> (git commit) -------> (git push)    
|_________________|________________|
        git reset --hard HEAD
|_________________|________________|___________________|
                git reset --hard origin/main               
```

# Remote Repository

## Clone Repository

To clone a remote repository locally, use the `git clone [url]` command. For example:

```shell
git clone https://github.com/hanjie-chen/PersonalArticles.git
```

This command creates a local folder with the same name and downloads the contents of the remote repository into it.

```shell
~ # git clone https://github.com/hanjie-chen/PersonalArticles.git
Cloning into 'PersonalArticles'...
remote: Enumerating objects: 1329, done.
remote: Counting objects: 100% (478/478), done.
remote: Compressing objects: 100% (357/357), done.
remote: Total 1329 (delta 166), reused 401 (delta 101), pack-reused 851 (from 1)
Receiving objects: 100% (1329/1329), 110.04 MiB | 39.59 MiB/s, done.
Resolving deltas: 100% (416/416), done.
~ # ls
PersonalArticles
```

> [!note]
>
> For now, you can think of this as downloading the remote repository locally. However, it is more complete than an ordinary download because it also includes the Git history and repository metadata.

To specify the destination folder, append its path to the end of the `git clone` command. For example:

```bash
git clone https://github.com/hanjie-chen/PersonalArticles.git ./articles-data
```

> [!note]
>
> If the destination directory already exists, it must usually be empty.

### Shallow Clone

Sometimes, we only need the current code from a remote repository—for example, when working with a knowledge base repository—and do not need its history. In that case, use the following command to retrieve only the current content:

```shell
git clone --depth 1 [url]
```

By default, `git clone` downloads the repository’s complete history.

The `--depth 1` option tells Git to retrieve only the latest commit state and omit all earlier history.

This reduces the storage space and download time required for the repository’s historical changes.

### HTTPS vs. SSH

There are two ways to run `git clone`: using HTTPS or SSH. For example:

```bash
git clone https://github.com/hanjie-chen/PersonalArticles.git
git clone git@github.com:hanjie-chen/PersonalArticles.git
```

The difference between these two methods lies in how authentication works:

- HTTPS: When running `git push`, you need additional authentication, such as signing in through a browser or using a token.
- SSH: Relies on a local SSH key and the corresponding public key configured on GitHub.

In general, the second method is preferable: configure an SSH key and use Git over SSH.

## Manage Remote

### View Remote

Use the following command to view the remote URLs associated with the current Git repository:

```bash
git remote -v
```

After running the command, you will see output similar to this:

```
origin  https://github.com/username/repository.git (fetch)
origin  https://github.com/username/repository.git (push)
```

Here, `origin` is the default remote name, followed by the remote repository’s URL. If you have multiple remote repositories, they will all be listed here.

### Change Remote URL

If the repository name on GitHub has changed, or if you want to switch the remote from HTTPS to SSH, use the following command to update the remote repository URL:

```shell
git remote set-url origin https://github.com/username/new-repo-name.git
# or
git remote set-url origin git@github.com:username/new-repo-name.git
```
