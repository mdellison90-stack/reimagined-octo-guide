# Advanced Git Commands Guide

This guide explains advanced Git commands and their usage with practical examples. These commands are essential for managing complex workflows and handling various scenarios in Git.

## Table of Contents
- [git stash](#git-stash)
- [git cherry-pick](#git-cherry-pick)
- [git revert](#git-revert)
- [git reset](#git-reset)

---

## git stash

The `git stash` command temporarily saves changes that you don't want to commit immediately. It's useful when you need to switch branches but have uncommitted work.

### Basic Usage

**Stash current changes:**
```bash
git stash
```
This saves your modified tracked files and staged changes, then reverts the working directory to match the HEAD commit.

**Stash with a descriptive message:**
```bash
git stash push -m "WIP: implementing user authentication"
```

**List all stashes:**
```bash
git stash list
```
Output example:
```
stash@{0}: WIP: implementing user authentication
stash@{1}: On main: fixing bug in login
```

**Apply the most recent stash:**
```bash
git stash apply
```

**Apply a specific stash:**
```bash
git stash apply stash@{1}
```

**Apply and remove the stash:**
```bash
git stash pop
```

**Remove a specific stash:**
```bash
git stash drop stash@{0}
```

**Clear all stashes:**
```bash
git stash clear
```

### Advanced Stash Options

**Stash including untracked files:**
```bash
git stash -u
# or
git stash --include-untracked
```

**Stash everything including ignored files:**
```bash
git stash -a
# or
git stash --all
```

**Create a branch from a stash:**
```bash
git stash branch new-feature-branch stash@{0}
```

### Practical Example

```bash
# You're working on a feature
$ echo "new feature code" >> feature.js
$ git add feature.js

# Urgent bug fix needed on main branch
$ git stash push -m "WIP: new feature implementation"

# Switch to main branch and fix the bug
$ git checkout main
$ # ... fix the bug and commit ...

# Return to your feature branch
$ git checkout feature-branch
$ git stash pop

# Continue working on your feature
```

---

## git cherry-pick

The `git cherry-pick` command applies the changes from specific commits to your current branch. It's useful for applying bug fixes or specific features from one branch to another.

### Basic Usage

**Cherry-pick a single commit:**
```bash
git cherry-pick <commit-hash>
```

**Cherry-pick multiple commits:**
```bash
git cherry-pick <commit-hash-1> <commit-hash-2>
```

**Cherry-pick a range of commits:**
```bash
git cherry-pick <start-commit-hash>..<end-commit-hash>
```

### Options

**Cherry-pick without committing:**
```bash
git cherry-pick -n <commit-hash>
# or
git cherry-pick --no-commit <commit-hash>
```
This applies the changes but doesn't create a commit, allowing you to modify or combine changes.

**Continue after resolving conflicts:**
```bash
git cherry-pick --continue
```

**Abort the cherry-pick:**
```bash
git cherry-pick --abort
```

**Edit the commit message:**
```bash
git cherry-pick -e <commit-hash>
# or
git cherry-pick --edit <commit-hash>
```

### Practical Example

```bash
# View commits on develop branch
$ git log develop --oneline
a1b2c3d Fix critical security vulnerability
e4f5g6h Add new user dashboard
i7j8k9l Update dependencies

# You're on main branch and need only the security fix
$ git checkout main
$ git cherry-pick a1b2c3d

# The commit a1b2c3d is now applied to main branch
```

**Example with conflict resolution:**
```bash
$ git cherry-pick a1b2c3d
# CONFLICT (content): Merge conflict in auth.js
# Automatic cherry-pick failed

# Resolve conflicts in auth.js
$ vim auth.js
# ... resolve conflicts ...

$ git add auth.js
$ git cherry-pick --continue
```

---

## git revert

The `git revert` command creates a new commit that undoes the changes from a previous commit. Unlike `git reset`, it doesn't alter the commit history, making it safe for shared branches.

### Basic Usage

**Revert a single commit:**
```bash
git revert <commit-hash>
```
This creates a new commit that reverses the specified commit's changes.

**Revert without creating a commit immediately:**
```bash
git revert -n <commit-hash>
# or
git revert --no-commit <commit-hash>
```

**Revert multiple commits:**
```bash
git revert <commit-hash-1> <commit-hash-2>
```

**Revert a range of commits:**
```bash
git revert <oldest-commit-hash>..<newest-commit-hash>
```

### Options

**Continue after resolving conflicts:**
```bash
git revert --continue
```

**Abort the revert:**
```bash
git revert --abort
```

**Revert a merge commit:**
```bash
git revert -m 1 <merge-commit-hash>
```
The `-m 1` option specifies which parent to use (1 for the first parent, usually the branch you merged into).

### Practical Example

```bash
# View commit history
$ git log --oneline
a1b2c3d (HEAD -> main) Add payment gateway
e4f5g6h Update user profile page
i7j8k9l Fix login bug

# The payment gateway commit introduced a bug
$ git revert a1b2c3d

# This creates a new commit that undoes changes from a1b2c3d
# History now looks like:
$ git log --oneline
x9y8z7w (HEAD -> main) Revert "Add payment gateway"
a1b2c3d Add payment gateway
e4f5g6h Update user profile page
i7j8k9l Fix login bug
```

**Example reverting multiple commits:**
```bash
# Revert the last 3 commits without creating individual commits
$ git revert --no-commit HEAD~2..HEAD
$ git commit -m "Revert recent changes due to critical bug"
```

---

## git reset

The `git reset` command moves the current branch to a specified commit and optionally modifies the staging area and working directory. **Warning:** This rewrites history and should be used carefully, especially on shared branches.

### Three Modes

#### 1. Soft Reset (`--soft`)
Moves HEAD to the specified commit but keeps changes in the staging area.

```bash
git reset --soft <commit-hash>
```

**Use case:** Undo commits but keep all changes staged for a new commit.

**Example:**
```bash
$ git log --oneline
a1b2c3d (HEAD -> main) Third commit
e4f5g6h Second commit
i7j8k9l First commit

$ git reset --soft e4f5g6h

# HEAD is now at e4f5g6h
# Changes from a1b2c3d are staged and ready to commit
$ git status
On branch main
Changes to be committed:
  modified:   file.txt
```

#### 2. Mixed Reset (`--mixed`, default)
Moves HEAD and updates the staging area, but keeps changes in the working directory.

```bash
git reset <commit-hash>
# or explicitly
git reset --mixed <commit-hash>
```

**Use case:** Undo commits and unstage changes, but keep them in your working directory.

**Example:**
```bash
$ git reset e4f5g6h

# HEAD is now at e4f5g6h
# Changes from a1b2c3d are in working directory but not staged
$ git status
On branch main
Changes not staged for commit:
  modified:   file.txt
```

#### 3. Hard Reset (`--hard`)
Moves HEAD and discards all changes in staging area and working directory.

```bash
git reset --hard <commit-hash>
```

**⚠️ Warning:** This permanently deletes uncommitted changes.

**Example:**
```bash
$ git reset --hard e4f5g6h

# HEAD is now at e4f5g6h
# All changes from a1b2c3d are permanently deleted
$ git status
On branch main
nothing to commit, working tree clean
```

### Common Use Cases

**Undo the last commit (keep changes):**
```bash
git reset HEAD~1
```

**Undo the last commit (discard changes):**
```bash
git reset --hard HEAD~1
```

**Unstage a file:**
```bash
git reset HEAD <file>
```

**Unstage all files:**
```bash
git reset HEAD
```

**Reset to a remote branch:**
```bash
git reset --hard origin/main
```

### Practical Example

```bash
# You made 3 commits but want to combine them into one
$ git log --oneline
a1b2c3d (HEAD -> main) Fix typo
e4f5g6h Add documentation
i7j8k9l Implement feature

# Reset to before the 3 commits, keeping changes staged
$ git reset --soft HEAD~3

# All changes are now staged
$ git status
On branch main
Changes to be committed:
  new file:   feature.js
  new file:   docs.md
  modified:   README.md

# Create a single commit with all changes
$ git commit -m "Implement feature with documentation"
```

### Recovery from Hard Reset

If you accidentally use `git reset --hard`, you might be able to recover using:

```bash
# View reflog to find the commit you reset from
$ git reflog

# Reset back to that commit
$ git reset --hard <commit-hash-from-reflog>
```

---

## Best Practices

1. **git stash**: Use descriptive messages with `git stash push -m` to easily identify stashed changes later.

2. **git cherry-pick**: Use for specific commits that need to be applied to other branches. Avoid cherry-picking too many commits; consider merging instead.

3. **git revert**: Preferred method for undoing changes on shared branches since it preserves history.

4. **git reset**: Use with caution, especially `--hard`. Never use on shared branches unless you coordinate with your team. Use `git revert` instead for public branches.

## Summary

| Command | Purpose | Modifies History | Safe for Shared Branches |
|---------|---------|------------------|--------------------------|
| `git stash` | Temporarily save changes | No | Yes |
| `git cherry-pick` | Apply specific commits | Creates new commits | Yes |
| `git revert` | Undo commits by creating inverse commit | No | Yes |
| `git reset` | Move branch pointer and optionally modify staging/working | Yes | No (use with caution) |

## Additional Resources

- [Official Git Documentation](https://git-scm.com/doc)
- [Pro Git Book](https://git-scm.com/book/en/v2)
- [Git Reference Manual](https://git-scm.com/docs)
