# Git and GitHub Version Control

## What is Version Control?

### Definition
Version control is a system that keeps track of every change made to files in a project. It allows teams to collaborate effectively without overwriting each other's work.

### Code Example
```bash
# Basic version control workflow
git init          # Initialize a new repository
git add .         # Stage all changes
git commit -m "Initial commit"  # Save snapshot with message
```

### Key Benefits
- **Full History**: Every change shows what changed, when, who did it, and why
- **Rollback Capability**: Can revert to previous working versions
- **Collaboration**: Multiple people can work on the same code simultaneously
- **Backup**: Remote copies serve as backup in case local machine fails

### Why It Matters
Every professional software team uses version control. Git is the most popular system, and understanding it is essential for modern development work.

## Git vs GitHub

### Git
Git is a program that runs locally on your computer and tracks project history. It works offline and was created by Linus Torvalds in 2005 for managing Linux kernel development.

### GitHub
GitHub is an online service that hosts Git repositories. It provides collaboration tools like pull requests, code review, issues, and project boards.

### Key Distinction
Think of Git as a camera taking pictures, and GitHub as an online photo album where you share those pictures with others.

## Core Git Concepts

### Repository (Repo)
A project folder that Git keeps track of. Every repository contains the complete history of all changes made to files within it.

### Commit
A saved snapshot of the entire project at a specific moment. Commits typically include a message describing what changed and why.

### Branch
A separate line of work that allows you to experiment without affecting the main codebase. The main branch should always contain working, production-ready code.

### Remote
A copy of the repository stored elsewhere, usually on GitHub. The default remote is typically named "origin".

### Clone
Downloading a repository from a remote location (like GitHub) to your local computer.

### Push
Sending commits from your local repository to a remote repository (GitHub).

### Pull
Getting new commits from a remote repository and merging them into your current branch.

### Merge
Combining changes from two different branches into one.

### Pull Request
A request to merge changes from one branch into another, usually with code review by team members.

## The Three Places for Your Code

Git organizes code in three locations:

1. **Working Directory**: Your actual files that you edit (like in Visual Studio Code)
2. **Staging Area**: A waiting room where you choose which changes will be included in the next commit
3. **Repository**: The complete history of all commits saved in Git

### Why This Three-Step Process?
The staging area allows you to selectively include only specific changes in each commit, rather than committing everything at once.

### Code Example
```bash
# Example workflow
git add file1.txt         # Add specific file to staging area
git add .                 # Add all changed files to staging area  
git commit -m "Fixed bug" # Commit staged changes with message

# If you want to see what would be committed:
git diff --cached         # Shows staged changes
```

### Gotcha: What Happens When You Don't Stage All Files?
If you change three files but only run `git add` on one file, then `git commit`, only the staged file will be included in the commit. The other two changes remain in the working directory.

## Basic Git Setup

### Initial Configuration
```bash
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"
git config --global init.defaultBranch main  # Set default branch name to 'main'
```

### Important Rules
- Use the same email for Git configuration as your GitHub account
- The `init.defaultBranch` setting ensures consistent naming (main instead of master)
- These configurations are done once per computer

## Repository Creation Methods

### Method 1: Create New Repository
```bash
git init                  # Creates new empty repository in current directory
```

### Method 2: Clone Existing Repository
```bash
git clone https://github.com/username/repository.git  # Downloads existing repo
```

### When to Use Which?
When joining a company, you almost always use `git clone` because the project already exists on GitHub. Only use `git init` for completely new projects.

## Everyday Git Commands

### Essential Commands (90% of daily use)
```bash
git status                # Shows changed files and current branch
git add <filename>        # Stages specific file changes
git add .                 # Stages all changes (be careful!)
git commit -m "message"   # Saves staged changes with message
git log                   # Shows commit history
git diff                  # Shows exact line differences
```

### Understanding Git Status
```bash
git status                # Shows:
                          # - Current branch
                          # - Changed files
                          # - Staged files
                          # - Untracked files
```

### Commit Message Best Practices
- Write clear, descriptive messages that explain the "what" and "why"
- Use imperative mood (fix bug, add feature, remove unused code)
- Keep commits small and focused on one change

### Example Good vs Bad Messages
```bash
# Bad commit message
git commit -m "Fixed stuff"

# Good commit message  
git commit -m "Fix incorrect total calculation when cart is empty"
```

## Git Ignore Files

### Purpose
Some files should never be tracked by Git, such as:
- Generated cache files (pycache)
- Virtual environments (.venv)
- Log files (.log)
- Environment files (.env) containing secrets

### Code Example
```bash
# Create .gitignore file in project root
echo ".venv" > .gitignore
echo "__pycache__" >> .gitignore
echo "*.log" >> .gitignore
echo ".env" >> .gitignore
```

### Security Warning
Never commit sensitive files like `.env` that contain passwords, API keys, or database connections. Even if you delete them later, they remain in Git history forever.

## Working with Remotes

### Remote Management
```bash
git remote -v             # Shows all remotes (like origin)
git push -u origin main   # Pushes to GitHub and sets upstream tracking
git pull                  # Downloads new commits and merges them
git fetch                 # Downloads but doesn't merge changes
```

### Key Points About Remotes
- Commits are local only until you push to a remote
- Your teammates can't see your work until you push it
- The default remote is usually called "origin"
- Use `git push -u origin branchname` for first-time pushes to set up tracking

## Branching Strategy

### What is a Branch?
A branch represents a separate line of development. This allows you to work on features or fixes without affecting the main codebase.

### Basic Branch Operations
```bash
git branch                # List all branches
git switch -c feature/name  # Create and switch to new branch
git switch branch-name    # Switch to existing branch  
git merge branch-name     # Merge another branch into current branch
git branch -d branch-name # Delete completed branch
```

### Branch Workflow Example
```bash
# Typical workflow for a new feature
git switch main           # Start from latest main
git pull                  # Get latest changes
git switch -c feature/search  # Create new branch
# Work and commit changes
git push origin feature/search  # Push branch to GitHub
# Open pull request on GitHub
# After review and merge:
git switch main           # Return to main
git pull                  # Update local main
git branch -d feature/search  # Delete feature branch
```

### Branch Best Practices
1. **One Feature Per Branch**: Don't mix unrelated changes in the same branch
2. **Keep Branches Short-Lived**: Usually days, not weeks or months
3. **Name Branches Clearly**: Use descriptive names with prefixes like `feature/`, `bugfix/`
4. **Pull Main Regularly**: Bring latest main into your branch to minimize conflicts

## Feature Branch Workflow

### Complete Process
1. Start from latest main: `git switch main && git pull`
2. Create feature branch: `git switch -c feature/new-feature`
3. Work and commit in small pieces
4. Push branch: `git push origin feature/new-feature`
5. Open pull request on GitHub
6. Team reviews code, suggests changes if needed
7. Merge into main after approval
8. Delete feature branch locally and remotely

### Why This Workflow?
- Main should always contain working, production-ready code
- Feature branches allow experimentation without risk to main
- Pull requests enable code review and collaboration
- Ensures clean history on main branch

## Pull Requests (PRs)

### What is a PR?
A pull request is a page on GitHub that says "Here are my changes. Please review and merge them into main."

### Good PR Characteristics
- Clear title and description
- Explain what was changed and why
- Include testing instructions if needed
- Link to related issues or user stories

### Review Process
1. Reviewer examines code for bugs, readability, and adherence to requirements
2. Reviewer can comment on specific lines
3. Author addresses feedback with new commits
4. PR updates automatically with changes
5. Approved PR gets merged into main

## Merging Strategies

### Three Merge Options in GitHub
1. **Create Merge Commit**: Preserves full history with extra commit
2. **Squash and Merge**: Combines all branch commits into one clean commit
3. **Rebase and Merge**: Moves commits on top of main without merge commit

### When to Use Each Strategy
- **Merge Commit**: When you want to see the complete history
- **Squash and Merge**: For cleaner main branch history (most popular)
- **Rebase and Merge**: When you want a straight-line history

## Handling Merge Conflicts

### What Happens During Conflicts?
When two branches modify the same line of code differently, Git can't automatically decide which version to keep.

### Conflict Resolution Example
```bash
# Example conflict in file.txt:
<<<<<<< HEAD
Welcome to our web shop
=======
Welcome to the shop
>>>>>>> branch-name

# Resolution:
Welcome to our web shop  # Choose one version or combine
```

### Fixing Conflicts
1. Open conflicting file
2. Decide which version(s) to keep
3. Remove conflict markers (`<<<<<<<`, `=======`, `>>>>>>>`)
4. Save file
5. `git add <file>` 
6. `git commit` to complete merge

### Important Notes
- Merge conflicts are normal and safe
- Git asks for human decision, doesn't guess
- Visual Studio Code provides helpful buttons for conflict resolution
- GitHub can resolve simple conflicts in browser

## Best Practices for Avoiding Conflicts

### Preventive Measures
1. **Pull main regularly**: Switch to main and pull before working on your branch
2. **Keep branches short-lived**: Days, not weeks/months
3. **Communicate with team**: Use daily scrum to coordinate work
4. **Don't reformat entire files**: Changes to indentation affect many lines
5. **Commit and push often**: Make work visible to team

### Example of Good Branch Management
```bash
# Before starting feature work:
git switch main
git pull                  # Get latest code
git switch -c feature/login-page  # Create new branch

# Regularly bring in changes from main:
git switch main
git pull
git switch feature/login-page
git merge main            # Resolve small conflicts early

# Work on feature and commit frequently:
git add .
git commit -m "Add login form"
git push origin feature/login-page  # Share progress
```

## GitHub Branch Protection

### What It Is
Branch protection rules prevent accidental breaking of main branch by requiring specific conditions before merging.

### Common Rules
- No direct pushes to main allowed
- Minimum number of approvals required
- All automated checks (tests) must pass
- Require pull request review before merge

### How to Set Up
Go to Repository Settings → Branches → Add Rule for "main" branch

## Git and Scrum Integration

### Connecting Concepts
- **User Story** becomes **Issue** on GitHub
- **Sprint Backlog** becomes **GitHub Project Board**
- **Feature Branch** is created for each story
- **Pull Request** represents work being reviewed
- **Merge** represents completing the story

### Workflow Example
1. Create issue #12: "Implement user login"
2. Create branch `feature/12-user-login`
3. Work and commit changes
4. Push to GitHub and create PR
5. Review and merge into main
6. Issue automatically closes when PR merges

## Undoing Changes in Git

### Safe Undo Commands
```bash
git restore <file>        # Discard changes since last commit
git restore --staged <file>  # Remove file from staging area (keep changes)
git commit --amend        # Fix last commit message or add missing files
git revert <commit-hash>  # Create new commit that undoes old changes
```

### Dangerous Commands (Use with Caution)
```bash
git reset --hard          # Deletes work permanently (don't use on shared branches)
git push --force          # Rewrites history (dangerous for shared repositories)
```

## Learning Resources

### Interactive Learning Platform
[Learn Git Branching](https://learngitbranching.js.org/) - Visual way to practice Git commands and understand branching concepts.

### Key Concepts to Remember
1. Git saves the history of your code
2. GitHub shares it with your team every day
3. Basic workflow: `git status`, `git add`, `git commit`, `git push`, `git pull`
4. Commit small changes with clear messages
5. Never commit secrets or sensitive information
6. New work equals new branch
7. Main always works (production-ready)
8. Feature branches, then pull request, review, and merge
9. Merge conflicts are questions, not errors

## Common Gotchas and Edge Cases

### File Staging Behavior
- `git add .` stages everything including files you might not want to commit
- Files in staging area show as "Changes to be committed"
- Files in working directory show as "Changes not staged for commit"

### Branch Names and Conventions
- Always use descriptive branch names that tell the team what's happening
- Follow your team's naming conventions (feature/, bugfix/, hotfix/)
- Include issue numbers when appropriate

### Remote Tracking
- After first push, `git push` works without specifying remote/branch
- Use `git push -u origin branch-name` to set upstream tracking
- `git pull` is equivalent to `git fetch` + `git merge`

### Conflict Resolution Tips
- Visual Studio Code makes conflict resolution easier with buttons
- Simple conflicts can be resolved in GitHub browser interface
- Complex conflicts require manual editing and careful consideration
- Always test code after resolving conflicts

This comprehensive coverage of Git and GitHub ensures you understand both the theoretical foundations and practical applications essential for modern software development teamwork.