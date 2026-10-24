# Git and GitHub Version Control

## Introduction to Version Control

### What is Version Control?
Version control is a system that keeps track of every change made to files over time. It provides four key benefits:
1. **Full History** - Every change shows what changed, when, who did it, and why
2. **Rollback Capability** - You can return to previous working versions if something breaks
3. **Collaboration** - Multiple people can work on the same code simultaneously without conflicts
4. **Backup** - Remote copies (like GitHub) serve as backups in case local data is lost

### Why Use Version Control?
Without version control, teams often end up with files like "version2_final_v2" and "final_version" where it's unclear which is the latest or what changed between versions. Git solves this by automatically tracking all changes.

```python
# Example of manual version control problems
# Instead of:
# project_v1.py
# project_v2.py  
# project_final.py
# project_final_updated.py
# Git tracks everything automatically with clear history
```

## Git vs GitHub

### Git
Git is a distributed version control system that runs locally on your computer. It was created by Linus Torvalds in 2005 for managing Linux kernel development. Git works offline - you can commit changes even without internet connection.

### GitHub
GitHub is a web-based platform that hosts Git repositories online. It provides collaboration features like:
- Pull requests and code reviews
- Issue tracking  
- Project boards
- Continuous integration tools

**Key Difference**: Git = camera, GitHub = online photo album where you share photos with others.

## Essential Git Vocabulary

### Core Terms
- **Repository/Repo** - The project folder that Git tracks
- **Commit** - A saved snapshot of the project with a descriptive message
- **Branch** - A separate line of development work
- **Remote** - A copy of the repository stored elsewhere (usually GitHub)
- **Clone** - Downloading a repository to your local computer
- **Push** - Sending commits to the remote repository
- **Pull** - Getting new commits from the remote repository
- **Merge** - Combining changes from different branches
- **Pull Request** - A request to merge branch changes into another branch (usually main)

## The Three States of Git

Git manages code in three distinct locations:

### Working Directory
The files you're actively editing (like those in Visual Studio Code).

### Staging Area (Index)
A "waiting room" where you select which changes will be included in the next commit. You use `git add` to move changes here.

### Repository (History)
All committed snapshots of your project stored locally. You use `git commit` to save staged changes as a new snapshot.

### Why This Three-Step Process?
This extra step allows you to:
- Commit only specific changes rather than everything at once
- Group related changes together logically
- Build up commits incrementally

```bash
# Example workflow
# Edit files in working directory
git status              # Check what changed
git add file1.py        # Stage specific file
git add .               # Stage all changes (be careful!)
git commit -m "Fixed bug" # Save staged changes as snapshot
```

### Understanding Git Add vs Git Commit
If you change three files but only run `git add` on one file, then `git commit`, only the one file that was added will be committed. The other two changes remain in the working directory and aren't committed yet.

```bash
# Scenario: Changed 3 files but only staged 1
git add file1.py        # Only this file gets staged
git commit -m "Fix bug" # Only file1.py is committed

# The other 2 files are still in working directory
# You can continue editing them or add them later
```

## Basic Git Setup and Commands

### Initial Configuration
Every computer needs to be configured with your identity before using Git:

```bash
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"
git config --global init.defaultBranch main
```

**Important**: Use the same email as your GitHub account for commit attribution.

### Repository Creation Methods

#### git init
Creates a new empty Git repository in the current folder. Used only for brand new projects.

```bash
mkdir my-project
cd my-project
git init
```

#### git clone
Downloads an existing repository from GitHub with its complete history. Used when joining a company or working on existing code.

```bash
git clone https://github.com/user/repo.git
```

### Everyday Git Commands

#### git status
Shows which files have changed, which are staged for commit, and what branch you're on.

```bash
git status
# Output shows:
# On branch main
# Changes to be committed: [staged files]
# Changes not staged for commit: [unstaged files]
```

#### git add
Stages changes for the next commit. Can stage individual files or all files.

```bash
git add file1.py        # Stage specific file
git add .               # Stage all files (careful!)
git add folder/         # Stage all files in folder
```

#### git commit
Saves staged changes as a new snapshot with a descriptive message.

```bash
git commit -m "Fixed login bug"
git commit -am "Updated documentation"  # Commit and add all tracked files at once
```

#### git log
Shows the commit history with each commit's ID, author, date, and message.

```bash
git log                 # Show full history
git log --oneline       # Show one line per commit (short format)
```

#### git diff
Shows exactly which lines have changed between working directory and staging area.

```bash
git diff                # Shows unstaged changes
git diff --staged       # Shows staged changes
```

### Git Workflow Rhythm
The typical daily workflow is:
1. Edit files in working directory
2. Run `git status` to see what's changed
3. Run `git add` to stage desired changes
4. Run `git commit` to save snapshot

This cycle repeats many times per day.

## Commit Best Practices

### Writing Good Commit Messages
Commit messages should be descriptive and follow a consistent style:

```bash
# Bad commit message
git commit -m "Fixed stuff"

# Good commit message  
git commit -m "Fix incorrect total calculation when cart is empty"
```

**Good practices for commit messages:**
1. Use present tense ("Add feature" not "Added feature")
2. Start with an action verb (add, fix, remove, update)
3. Be specific about what changed and why
4. Complete the sentence: "If applied, this commit will [action]"

### Small, Focused Commits
- Commit often in small pieces
- One commit = one logical change
- Small commits are easier to understand, review, and undo

```bash
# Good: Multiple small commits
git add bugfix.py
git commit -m "Fix null pointer exception in login"

git add feature.py  
git commit -m "Add user registration functionality"
```

## Ignoring Files with .gitignore

### What to Ignore
Some files should never be committed to Git because they contain sensitive information or are automatically generated:

```bash
# Common entries in .gitignore
__pycache__/            # Python cache files
.env                    # Environment variables (contains secrets!)
venv/                   # Virtual environment folders
*.log                   # Log files
.DS_Store               # macOS metadata files
```

### Security Concerns with Secrets
Never commit passwords, API keys, or database connection strings. Even if you delete them later, they remain in Git history forever.

```bash
# Example of dangerous .env file content
DATABASE_URL=postgresql://user:password@localhost/mydb
API_KEY=sk-1234567890abcdef
```

**Important**: If you accidentally commit secrets, change them immediately and remove the old versions from history.

## Working with Remotes

### Remote Repositories
A remote is a copy of your repository stored elsewhere (typically GitHub). The default remote name is "origin".

```bash
git remote -v           # Show all remotes
git remote add origin https://github.com/user/repo.git  # Add new remote
```

### Push and Pull Operations

#### git push
Sends local commits to the remote repository:

```bash
# First time pushing new branch
git push -u origin feature-branch

# Subsequent pushes (no -u needed)
git push
```

#### git pull
Downloads new commits from remote and merges them into your current branch:

```bash
git pull                # Download and merge changes
git fetch               # Download but don't merge
```

### Key Points About Push/Pull
- **Commits are local only** until you push to GitHub
- Teammates can't see your work until you push
- Always push before closing your laptop or going offline

## Branching Strategy

### What is a Branch?
A branch is a separate line of development work. It allows you to experiment without affecting the main codebase.

```bash
# Visual example:
# Main branch: A → B → C
# Feature branch: A → B → C → D → E
```

### Why Use Branches?
1. **Main stays stable** - Only tested, working code goes into main
2. **Safe experimentation** - You can make mistakes on branches without breaking everyone else
3. **Parallel development** - Multiple features can be worked on simultaneously

### Branch Workflow Pattern
Most companies follow this pattern:

1. **Start from latest main**
   ```bash
   git checkout main
   git pull
   ```

2. **Create feature branch**
   ```bash
   git switch -c feature/search
   ```

3. **Work and commit regularly**
   ```bash
   # Make changes, commit often
   git add .
   git commit -m "Add search functionality"
   ```

4. **Push branch to GitHub**
   ```bash
   git push -u origin feature/search
   ```

5. **Create Pull Request** on GitHub

6. **Team reviews and approves**

7. **Merge into main**

8. **Delete feature branch**
   ```bash
   git checkout main
   git pull
   git branch -d feature/search
   ```

### Branch Naming Conventions
Branch names should clearly communicate what they're for:

```bash
# Good naming patterns
feature/user-login
bugfix/payment-error
hotfix/critical-security-fix
refactor/database-connection

# With issue numbers
feature/12-order-history
bugfix/45-login-validation
```

### Branch Best Practices
1. **One feature per branch** - Don't mix unrelated changes
2. **Keep branches short-lived** - Days, not weeks or months
3. **Pull main regularly** - Merge latest changes to reduce conflicts
4. **Use descriptive names** - Clear and consistent naming

## Pull Requests and Code Review

### What is a Pull Request?
A pull request is GitHub's way of proposing changes from one branch to another (usually main). It serves as:
- A review mechanism for code quality
- A discussion forum for improvements
- A way to ensure changes meet project standards

### PR Best Practices
**For PR Author:**
- Keep pull requests small (few hundred lines max)
- Write clear title and description explaining what changed, why, and how to test
- Link to relevant issues using "Closes #123" syntax
- Address all review comments before merging

**For Reviewers:**
- Be kind and specific in feedback
- Focus on code quality, not personal style preferences
- Ask questions instead of giving orders
- Look for bugs, maintainability, and adherence to requirements

### Merge Strategies
GitHub offers three merge options:

1. **Create merge commit** - Preserves full history with an extra commit
2. **Squash and merge** - Combines all branch commits into one clean commit in main  
3. **Rebase and merge** - Moves commits on top of main without a merge commit

## Handling Merge Conflicts

### What Are Merge Conflicts?
Merge conflicts occur when two branches modify the same line of code differently. Git can't automatically decide which version is correct, so it stops and asks you to resolve the conflict manually.

```bash
# Example conflict in file.py
<<<<<<< HEAD
def calculate_total():
    return price * quantity + tax
=======
def calculate_total():
    return price * quantity + shipping_cost
>>>>>>> feature-branch
```

### Resolving Conflicts
1. **Open conflicting file** in your editor
2. **Choose correct version** or combine both
3. **Remove conflict markers** (`<<<<<<<`, `=======`, `>>>>>>>`)
4. **Save the file**
5. **Stage and commit** the resolved file

```bash
git add conflicted-file.py
git commit -m "Resolve merge conflict in calculate_total"
```

### Prevention Strategies
- Pull main often to stay up-to-date with changes
- Keep branches small and short-lived
- Communicate with team members about file changes
- Avoid reformatting large sections of code
- Commit and push frequently

## Git Workflow in Agile Scrum Context

### Connecting Git to Scrum
In a Scrum environment, the workflow connects to user stories:

1. **User Story becomes Issue** on GitHub
2. **Sprint Backlog becomes Project Board** with columns like To Do, In Progress, Review, Done
3. **Feature Branch** created for each story
4. **Pull Request** opened when work is complete
5. **Review and Approval** process completes the definition of done
6. **Merge** updates the board to Done

```bash
# Example workflow for user story #123
git switch -c feature/123-login-page
# Work on login functionality
git push -u origin feature/123-login-page
# Create PR with "Closes #123"
```

## Advanced Git Operations

### Undoing Changes

#### git restore
- `git restore file.py` - Discards changes in working directory since last commit
- `git restore --staged file.py` - Removes file from staging area while keeping changes

#### git commit --amend
Fixes the most recent commit (only before pushing):

```bash
git commit -m "Fix typo"     # Wrong message
git commit --amend -m "Fix typo correctly"
```

#### git revert
Creates a new commit that undoes previous changes. Safe for shared repositories:

```bash
git revert <commit-hash>
```

### Dangerous Operations to Avoid
- `git reset --hard` - Deletes commits permanently
- `git push --force` - Rewrites history, potentially losing others' work

## Learning Resources

### Interactive Learning Platform
[learngitbranching.js.org](https://learngitbranching.js.org) provides visual, interactive Git training that helps you understand branching concepts through hands-on practice.

### Key Takeaways
1. **Git saves your code history** - Every change is tracked automatically
2. **GitHub shares work with teams** - Collaboration happens through shared repositories  
3. **Use daily commands**: `status`, `add`, `commit`, `push`, `pull`
4. **Commit small with clear messages**
5. **Never commit secrets** or sensitive information
6. **New work = new branch** - Always create feature branches
7. **Main always works** - Only stable code goes into main branch
8. **Feature branch → pull request → review → merge** is the standard workflow
9. **Merge conflicts are questions, not errors** - They're normal and resolvable

### Recommended Practice
Before starting your group project on Monday, practice these concepts using the interactive learning platform. This will help solidify your understanding of Git branching before working collaboratively in teams.