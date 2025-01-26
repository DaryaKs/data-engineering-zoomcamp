This is my project while working on Data Engineering Zoomcamp.
Here I will have files to complete tasks during the learning process and store files of homework.

The link for the official course:
https://github.com/DataTalksClub/data-engineering-zoomcamp

This repository is valid for the DE course on January 2025.

# Setting Up Your Git Repository Locally

This guide provides step-by-step instructions to set up your Git repository locally, configure Git, and manage your commits and pushes effectively. It also includes handy commands for committing changes and handling common Git issues.

---

## Clone Your Repository and Configure Git**

### 1. Navigate to Your Desired Directory locally**

Open your terminal and navigate to the main location where you want to clone your repository to on your local machine. For example, on macOS:

```bash
cd /Users/userName/
```

### 2. Create a New Repository on GitHub**

Log in to your GitHub account.
Create a new repository, choosing either private or public visibility. For instance, name it *data-engineering-zoomcamp*.
After creating the repository, copy the HTTPS clone link, such as:
```bash
https://github.com/DaryaKs/data-engineering-zoomcamp.git
```

### 3. Verify Git Installation Locally**

Ensure that Git is installed on your system through running the command bellow in the Terminal:
```bash
git --version
```

If Git is not installed, follow these steps:
- Install Xcode Command Line Tools:
```bash
xcode-select --install
git --version
```
- Install Git via Homebrew (if necessary):
```bash
brew install git
git --version
```

### 4. Configure Git with Your Credentialsy**
After installing Git, set up your global Git configuration with your user details using Terminal:
```bash
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"
```

### 5. Clone Your Repository**
Clone your GitHub repository using the copied HTTPS link from step 2:
```bash
git clone <repository-url>
```
Note: If you receive a warning like You appear to have cloned an empty repository., it means the repository currently has no commits or files.

Git may prompt you for your GitHub username and password when pushing.
Note: GitHub has deprecated password authentication for Git operations. Use a Personal Access Token (PAT) instead of your password.

### 6. Configure VS Code for Git**
1. Install the Git Extension in VS Code:
    - Open VS Code.
    - Go to the Extensions panel (⇧⌘X or Shift + Command + X).
    - Search for "Git" and install the official Git extension.
2. Open Your Repository Folder in VS Code:
    - Use File > Open and navigate to your cloned repository folder (data-engineering-zoomcamp).

## Commit and Push Your Changes*

### 1. Navigate to Your Repository Directory*
Ensure you're in the correct repository directory before making changes, for instance:
```bash
cd /Users/userName/data-engineering-zoomcamp/
```

### 2. Check Repository Status*
View the current status of your repository to see untracked or modified files:
```bash
git status
```

Possible Output:
```bash
On branch master
No commits yet
nothing to commit (create/copy files and use "git add" to track)
```

### 3. Add Files and Make Your First Commit*
Then you can push your changes to the main branch, for example in VS Code you created new file filenewname.txt, then run:
```bash
git add filenewname.txt
git commit -m "new file filenewname.txt was added"
```

You can also create the file using Terminal:
Add a README File:
```bash
echo "# Data Engineering Zoomcamp" > README.md
```

Stage the README File:
```bash
git add README.md
```

Commit the Changes:
```bash
git commit -m "Initial commit with README"
```

### 4. Push the Commit to GitHub*
Push your committed changes to the main branch on GitHub:
```bash
git push
```

```bash
git push -u origin main
```

Note: If your default branch is master, replace main with master:
```bash
git push -u origin master
```


## Updating local from main**
To get changes from the main branch to your local instance:
```bash
git status
git fetch
git pull
```

## Git shows .DS_Store file as modified**
On MacOs do not open your git repo using finder, it could create system file: .DS_Store
Navigating to a folder using the "Finder" on Mac generates a .DS_Store file holding metadata about the folder (e.g. thumbnails etc.). These files can pollute your git commits and are annoying.
 - You can manually delete them from your folder:
    ```bash
    git status
    rm .DS_Store
    git status
    ```
 - If the file just keeps popping up. Luckily, you can add files of this kind to their global gitignore file to never commit these to their git projects:
    ```bash
    git status
    echo ".DS_Store" >> .gitignore
    git add .gitignore
    git commit -m "Add .DS_Store to .gitignore"
    git push
    git status
    ```

## Creating Empty Folders**
Creating Empty Folders
Git does not track empty folders. To include a folder, create a placeholder file:
```bash
touch folder_name/.gitkeep
```

Then add, commit, and push:
```bash
git add folder_name/.gitkeep
git commit -m "Add empty folder folder_name"
git push
```

# Regular Git Workflow Commands

## Check Current Directory**
Ensure you're in the correct directory:
```bash
pwd
```