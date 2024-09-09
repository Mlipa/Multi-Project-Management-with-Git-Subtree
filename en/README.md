
# Solution for organizing multiple subprojects with `git subtree`

My goal was to organize a local project with multiple parts (such as a frontend, backend, and a mobile app) that were well structured and synchronized with their respective base repositories. Additionally, I wanted to ensure that local changes would not directly affect the base repository. I needed a way to keep the structure organized, where each part of the project was isolated but still synchronized. Here's the solution to the problems I encountered and how I implemented this structure using `git subtree`.

## Problem Description

I wanted to create a project with a base repository, but I didn't want the changes in my new project to affect the original base repository. Moreover, this project needed to stay synchronized with the base repository. However, I encountered several issues when trying to use `--prefix=.` at the root of the project.

Here's the solution I found to manage this scenario, including how to synchronize the project with the base repository without affecting the local project structure.

## Why can't `--prefix=.` be used?

When you try to use `--prefix=.` to add the content of a repository directly into the root of a Git project, Git throws an error because the root directory already exists or contains files.

### Example errors:

```
fatal: prefix '.' already exists.
```
or
```
fatal: can't squash-merge: '.' was never added.
You need to run this command from the top level of the working tree.
```

These errors indicate that `git subtree` cannot be used directly in the root path because it already contains files or configurations, and the goal is to have the base code in the root.

## Solution

The solution is to create a main directory (project) that will contain the parts of the project (backend, frontend, mobile app, etc.) and use `git subtree` to manage the code of each subproject in isolation. This way, the main directory can manage the entire project, and each subproject will have its own root.

## Directory Structure

Below is an example of how you should organize your projects locally to maintain a clear structure for the solution:

```
my-projects
└───project
│   ├───project-backend
│   ├───project-frontend
│   └───project-app-mobile
│   .gitignore
│   readme.md (optional)
└───project-two
│   ├───project-two-backend
│   ├───project-two-frontend
│   └───project-two-app-mobile
│   .gitignore
│   readme.md (optional)
```

You can have as many projects as you like within this structure.

## Commands and Workflow to Set Up Multiple Projects Locally and Synchronize with GitHub Repositories

I'll go through the steps using an example where `main-backend` is the base repository I want to bring into a new project and keep synchronized using `git subtree`.

### 1. Create a Main Directory

First, I create a main directory and initialize Git within it.

```bash
mkdir project
cd project
git init
```

### 2. Add the Base Repository and Use `git subtree`

I add the remote repository for the backend I want to synchronize and use `git subtree` to bring the code into my subproject `project-backend`:

```bash
pwd 
# Current directory: project
git remote add main-backend https://github.com/your-user/repo-backend.git
git fetch main-backend
git subtree add --prefix=project-backend main-backend main --squash
```

#### ⚠️Note: If the following error occurs:

```
fatal: ambiguous argument 'HEAD': unknown revision or path not in the working tree.
Use '--' to separate paths from revisions, like this:
'git <command> [<revision>...] -- [<file>...]'
fatal: working tree has modifications.  Cannot add.
```

This error indicates that Git is detecting uncommitted changes in your repository (working tree has modifications), and it cannot find the `HEAD` reference, which often happens if you don't have an initial commit in your newly created repository. The solution is to create an empty commit:

```bash
git commit --allow-empty -m "Initial commit for backend"
```

Now re-run the `git subtree...` command.

### 3. Project Synchronization

To synchronize the changes from the base repository into the subproject, I return to the main directory containing the subprojects and perform a `pull`:

```bash
pwd
# Current directory: project
git subtree pull --prefix=project-backend main-backend main --squash
```

### 4. Repeat the Process for Multiple Projects

This workflow can be repeated for each additional project (Backend, Frontend, Mobile App, etc.).

## `.gitignore` Configuration

I recommend configuring a `.gitignore` file that ignores all files except for `README.md` and `.gitignore`, keeping your main project organized and preventing conflicts:

```gitignore
# Ignore everything except README.md and .gitignore
*
!.gitignore
!README.md
```

## 5. Push the Changes to a New Repository

Finally, I push the changes to the new repository that I created on GitHub. Make sure you're in the subproject that you want to push:

```bash
pwd
# Current directory: project/project-backend
git init
git add .
git commit -m "Your commit message"
git branch -M main
git remote add origin https://github.com/your-user/your-new-repo.git
git push -u origin main
```

Done! You now have your project synchronized with a base repository while keeping local changes isolated and not affecting the original repository. 😊🙌
