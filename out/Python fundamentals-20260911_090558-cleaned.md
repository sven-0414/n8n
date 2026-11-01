# Python and Git Fundamentals

## Introduction to Git and GitHub

### What is Git?
Git is a distributed version control system that tracks changes in files over time. It allows multiple developers to work on the same project without conflicts, keeps a history of all modifications, and enables collaboration by managing branches and merging changes.

```bash
# Initialize a new Git repository in current directory
git init
```

### What is GitHub?
GitHub is a web-based platform that hosts Git repositories. It provides a user-friendly interface for managing code, collaborating with others, and storing projects securely. It also offers features like issue tracking, pull requests, and project management tools.

### Why Use Git and GitHub?
- Keeps track of all changes made to your code
- Enables collaboration among multiple developers
- Provides backup and version control
- Helps maintain clean, organized project history
- Allows you to easily revert to previous versions if needed

## Setting Up a Git Repository

### Creating a New Folder and Initializing Git
To start working with Git, create a new folder for your project and initialize it as a Git repository.

```bash
# Create a new directory
mkdir git-demo

# Navigate into the directory
cd git-demo

# Initialize Git in this directory
git init
```

When you run `git init`, Git creates a hidden `.git` folder that stores all the version control information for your project.

### Creating Your First File
Before adding files to Git, create some content in your repository:

```bash
# Create a simple text file
echo "Hello, World!" > python.txt
```

This creates a file named `python.txt` with basic content. In practice, you'd be creating actual Python code files (`.py`) for your projects.

## Basic Git Workflow

### Adding Files to Staging Area
Before committing changes, you need to add files to the staging area using `git add`.

```bash
# Add a specific file
git add python.txt

# Add all files in current directory
git add .
```

The `git add` command prepares files for commit by placing them in the staging area. You can add individual files or use `.` to add everything in the current directory.

### Making Your First Commit
A commit is a snapshot of your project at a specific point in time with a descriptive message.

```bash
# Make a commit with a message
git commit -m "Add initial python.txt file"
```

Commit messages should be:
- Short (2-3 words maximum)
- Descriptive and meaningful
- Written in present tense
- Specific to what changed

### Setting Up Remote Repository
To push your local changes to GitHub, you need to link your local repository to a remote one.

```bash
# Add remote origin (replace with your actual GitHub URL)
git remote add origin https://github.com/username/repository-name.git
```

## Pushing to GitHub

### Configuring Default Branch Name
By default, Git uses `main` as the primary branch name. You can set this explicitly:

```bash
# Set default branch name to main
git branch -M main
```

### Pushing Changes to GitHub
After setting up your remote repository and configuring the branch, push your changes:

```bash
# Push commits to remote repository
git push -u origin main
```

The `-u` flag sets the upstream tracking reference, so future `git push` commands will automatically push to this branch.

## Best Practices for Git Usage

### Commit Message Guidelines
While instructors recommend short commit messages (2-3 words), professional developers often prefer more descriptive messages that explain:
- What was changed
- Why it was changed
- How it affects the project

Example good commit messages:
```bash
git commit -m "Fix login validation bug"
git commit -m "Add user authentication module"
git commit -m "Update README with installation instructions"
```

### Folder Structure for Labs
It's recommended to create one folder per lab, which keeps projects organized and makes it easier to track individual work.

```bash
# Example structure
lab-1/
  ├── main.py
  └── requirements.txt

lab-2/
  ├── script.py
  └── data.json
```

### Pushing Completed Labs
Always push your completed labs to GitHub with clear commit messages and proper folder organization:

```bash
# After completing a lab
git add .
git commit -m "Complete lab 1: Data processing functions"
git push origin main
```

## Troubleshooting Common Issues

### Setting Up Git Credentials
If you haven't configured your Git credentials, you'll need to do so before pushing:

```bash
# Set global user name and email
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"
```

### Handling Permission Errors
When working with private repositories or when GitHub asks for authentication, ensure your SSH keys are properly configured or use HTTPS with credentials.

### Repository Already Exists
If you're trying to push to an existing repository that already has content:

```bash
# If repository is empty
git push -u origin main

# If repository has content and you want to merge
git pull origin main
git push -u origin main
```

## Project Organization Tips

### Using .gitignore
While not mentioned in the lecture, `.gitignore` files are crucial for excluding unnecessary files from version control:

```bash
# Create .gitignore file
echo "*.pyc" > .gitignore
echo "__pycache__/" >> .gitignore
```

This prevents Python bytecode files and cache directories from being tracked.

### Repository Naming Conventions
Use clear, descriptive names for your repositories:
- `lab-1-data-processing`
- `assignment-2-web-app`
- `project-final-milestone`

These names help you and others quickly understand what each repository contains.