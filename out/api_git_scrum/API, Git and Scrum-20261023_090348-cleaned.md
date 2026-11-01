# Git and GitHub: Version Control and Collaborative Development

## Understanding Version Control

### What is Version Control?

Version control is a system that tracks changes to files over time, allowing teams to manage code development efficiently. It keeps track of every change made to a project, including who made the change, when it was made, and what the change was.

### Why Use Version Control?

1. **Full History Tracking**: Every change shows what changed, when, who did it, and why
2. **Rollback Capability**: You can revert to previous working versions if something breaks
3. **Collaboration**: Multiple people can work on the same code simultaneously without conflicts
4. **Backup**: Acts as a backup system - if your local computer fails, your code is safe on remote servers

### Git vs GitHub

**Git** is a program that runs locally on your computer and tracks changes to your project files. It works offline and was created by Linus Torvalds in 2005 for managing Linux kernel development.

**GitHub** is an online platform that stores Git repositories and provides tools for collaboration, including pull requests, code review, issues tracking, and project boards.

### Key Concepts

- **Repository (Repo)**: A project folder that Git keeps track of
- **Commit**: A saved snapshot of the whole project at a moment in time with a message describing the change
- **Branch**: A separate line of work that allows you to experiment without affecting the main codebase
- **Remote**: A copy of the repository stored elsewhere (usually on GitHub)
- **Clone**: Downloading a repository to your local computer
- **Push**: Sending commits to the remote repository
- **Pull**: Getting new commits from the remote repository
- **Merge**: Combining two branches together

## Git Workflow and Basic Commands

### The Three Places for Your Code

Git works with three distinct locations:
1. **Working Directory**: Files you're currently editing in your code editor
2. **Staging Area**: A "waiting room" where you prepare changes before committing
3. **Repository**: The history of all commits saved in Git

### Git Commands Overview

```bash
# Initialize a new repository (for brand new projects)
git init

# Clone an existing repository from GitHub
git clone <repository-url>

# Configure your Git identity
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"
git config --global init.defaultBranch main  # Sets default branch name to 'main'

# Check current status
git status

# Stage changes for commit
git add <file-name>          # Stage specific file
git add .                    # Stage all changes (use carefully)

# Commit staged changes with a message
git commit -m "Your commit message here"

# View commit history
git log

# See what changes have been made
git diff                     # Shows differences in unstaged files
git diff --staged            # Shows differences in staged files

# Push commits to remote repository
git push origin <branch-name>

# Pull new commits from remote repository
git pull

# Fetch new commits without merging
git fetch
```

### Why the Staging Area?

The staging area gives you control over what goes into each commit. Instead of committing everything at once, you can selectively choose which changes to include in each snapshot.

**Example**: If you fix a bug and start working on a feature:
```bash
# Fix the bug first
git add bug_fix_file.py
git commit -m "Fixed price rounding issue"

# Then work on the new feature
# (don't add the feature files yet)
# Continue working...
```

This approach ensures one commit = one logical change, making code review easier and allowing you to undo specific changes more easily.

## Best Practices for Git

### Commit Messages

Good commit messages should be:
- **Descriptive**: Explain what changed and why
- **Concise**: Brief but informative
- **Action-oriented**: Use verbs like add, fix, remove, update

**Bad examples**:
```
git commit -m "Fixed stuff"
git commit -m "Changed things"
```

**Good examples**:
```
git commit -m "Fixed wrong total when cart is empty"
git commit -m "Added category filter to products list"
```

### Commit Frequency and Size

- **Commit often**: Make small, frequent commits
- **Small commits**: Each commit should represent one logical change
- **Large commits**: Difficult to review, undo, or understand later

### Ignoring Files

Create a `.gitignore` file in your project root to exclude files that shouldn't be tracked:

```gitignore
# Python cache files
__pycache__/
*.pyc

# Virtual environment
venv/
.env
.env.local
.env.*.local

# Log files
*.log

# IDE specific files
.vscode/
.idea/
```

**Important**: Never commit sensitive information like passwords, API keys, or database credentials to version control. Even if you delete them later, they remain in the commit history.

### Working with Remotes

```bash
# Check remote repositories
git remote -v

# Push to remote (first time)
git push -u origin <branch-name>

# Push after setting upstream
git push

# Pull new changes from remote
git pull
```

## Branching Strategy

### What is a Branch?

A branch is a separate line of work that allows you to experiment with new features or fixes without affecting the main codebase. The `main` branch should always contain working, production-ready code.

### Branch Creation and Management

```bash
# List existing branches
git branch

# Create and switch to a new branch
git switch -c <branch-name>

# Switch to an existing branch
git switch <branch-name>

# Merge another branch into current branch
git merge <branch-name>

# Delete a merged branch
git branch -d <branch-name>
```

### Feature Branch Workflow

1. **Start from latest main**:
   ```bash
   git switch main
   git pull
   ```

2. **Create feature branch**:
   ```bash
   git switch -c feature/search
   ```

3. **Work and commit regularly**:
   ```bash
   # Make changes, then:
   git add .
   git commit -m "Add search functionality"
   ```

4. **Push branch to GitHub**:
   ```bash
   git push -u origin feature/search
   ```

5. **Create Pull Request**: On GitHub, open a PR from your feature branch to main

6. **Review and merge**: Team reviews code, makes comments, approves changes, then merges

7. **Clean up**:
   ```bash
   git switch main
   git pull
   git branch -d feature/search  # Delete local branch
   ```

### Branch Naming Conventions

Branch names should be descriptive and follow team conventions:
- `feature/add-login-page`
- `bugfix/fix-user-registration`
- `hotfix/critical-security-patch`

**Rules for branching**:
1. **One feature per branch**: Don't mix unrelated changes
2. **Keep branches short-lived**: Days, not weeks or months
3. **Pull main regularly**: Merge latest changes from main to avoid conflicts

## Pull Requests and Code Review

### What is a Pull Request?

A pull request (PR) is GitHub's way of proposing changes to be merged into the main branch. It allows team members to review code before merging.

### Pull Request Best Practices

**What to include in PR description**:
- Summary of what changed
- Why the change was needed
- How to test it
- Link to related issues or user stories

**Code Review Process**:
1. **Reviewer should be kind and specific**
2. **Ask questions instead of giving orders**
3. **Focus on code quality, not personal criticism**

### Merging Strategies

GitHub offers three merge options:
1. **Create Merge Commit**: Preserves all individual commits in history
2. **Squash and Merge**: Combines all commits into one clean commit
3. **Rebase and Merge**: Applies commits on top of main without a merge commit

## Handling Conflicts

### What Happens When Conflicts Occur?

Conflicts occur when two branches modify the same line in different ways. Git stops the process and asks you to resolve it manually.

**Example conflict in a file**:
```javascript
<<<<<<< HEAD
welcome to our web shop
=======
welcome to the shop
>>>>>>> feature-branch
```

### Resolving Conflicts

1. **Open the conflicted file**
2. **Decide which version(s) to keep**
3. **Remove conflict markers** (`<<<<<<<`, `=======`, `>>>>>>>`)
4. **Save the file**
5. **Stage and commit the resolved file**:
   ```bash
   git add <file-name>
   git commit -m "Resolve merge conflict"
   ```

### Preventing Conflicts

1. **Pull main regularly** to keep your branch up-to-date
2. **Keep branches small and short-lived**
3. **Communicate with team members** about what files you're working on
4. **Don't reformat entire files** - this creates unnecessary conflicts

## Advanced Git Operations

### Undoing Changes

Git provides several safe ways to undo changes:

```bash
# Unstage a file (remove from staging area but keep changes)
git restore --staged <file-name>

# Discard changes in working directory since last commit
git restore <file-name>

# Amend the last commit (use only before pushing)
git commit --amend -m "Corrected message"

# Create a new commit that undoes an old one
git revert <commit-hash>
```

### Dangerous Operations

Avoid these commands on shared branches:
- `git reset --hard`: Can delete committed changes
- `git push --force`: Can overwrite others' work

## Integration with Agile and Scrum

### Connecting Git to Scrum

In a Scrum environment:
- **User stories** become **GitHub issues**
- **Sprint backlog** becomes **GitHub project board**
- **Feature branches** are created for each story
- **Pull requests** represent the "Definition of Done"
- **Merging into main** closes the issue and completes the increment

### Example Workflow

1. **Create issue**: "Implement user login functionality"
2. **Create branch**: `feature/user-login`
3. **Work on feature**: Commit regularly
4. **Push to GitHub**: Create PR with description linking to issue
5. **Code review**: Team reviews and approves
6. **Merge**: PR merged into main
7. **Close issue**: Issue automatically closes when PR is merged

## Learning Resources

### Interactive Learning Platform

[Learn Git Branching](https://learngitbranching.js.org/) - Visual tool to practice Git commands interactively.

### Practice Commands

```bash
# Check what files are being tracked
git status

# Stage a folder (will stage all files within it)
git add <folder-name>

# See the current branch and recent commits
git log --oneline

# View changes in a specific commit
git show <commit-hash>
```

### Key Takeaways

1. **Git saves history** of your code changes
2. **GitHub shares** your code with your team
3. **Follow the workflow**: commit small, work on branches, use pull requests
4. **Never commit secrets** or sensitive information
5. **Main branch should always be stable**
6. **Pull main regularly** to avoid conflicts
7. **Code review is part of the process**

This comprehensive understanding of Git and GitHub will help you collaborate effectively with development teams and manage your codebase professionally.