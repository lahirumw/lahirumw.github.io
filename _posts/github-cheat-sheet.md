---
layout: post
title: GitHub Cheat Sheet - Master Your Workflow
subtitle: 
cover-img: 
thumbnail-img: 
share-img: /assets/img/path.jpg
tags: [Github, Git, Version Control, Git Commands]
author: keylearn
---

GitHub is a powerful tool for version control and collaboration, and knowing the right commands and workflows can save you time and effort. Here’s a cheat sheet to help you get the most out of GitHub, whether you’re a beginner or a pro.

## Basic Git Commands

**Setting Up**

**Configure Git**

~~~
git config --global user.name "Your Name" 
git config --global user.email "youremail@example.com"
~~~

**Check Configuration**

~~~
git config --list
~~~

**Starting a New Repository**

**Initialize a repository**

~~~~
git init
~~~~

**Clone a repository**

~~~~
git clone <repository-url>
~~~~

**Making Changes**

**Check status**

~~~~
git status
~~~~

**Add changes to the staging area**

~~~
git add <file>
~~~

**Add all files**

~~~
git add .
~~~

**Commit changes**

~~~~
git commit -m "Your commit message"
~~~~

**Pushing and Pulling**

**Push changes to remote repository**

~~~
git push origin <branch-name>
~~~

**Pull updates from remote repository**

~~~
git pull origin <branch-name>
~~~

**Branching and Merging**

**Working with Branches**
**Create a new branch**

~~~
git branch <branch-name>
~~~

**Switch to a branch**

~~~
git checkout <branch-name>
~~~

Or, create and switch in one step:

~~~
git checkout -b <branch-name>
~~~

**List all branches**

~~~
git branch
~~~

**Delete a branch**

~~~
git branch -d <branch-name>
~~~

**Merging Branches**
Merge a branch into the current branch

~~~
git merge <branch-name>
~~~

Resolve merge conflicts Edit conflicting files, then:

~~~
git add <file> git commit
~~~

**Collaboration**

**Forking and Pull Requests**

Fork a repository Click the “Fork” button on the repository’s GitHub page.
Clone your forked repository

~~~
git clone <your-fork-url>
~~~

Sync fork with original repository

~~~
git remote add upstream <original-repository-url> 
git fetch upstream git merge upstream/main
~~~

Create a pull request Push your changes to your forked repository, then click “Compare & pull request” on GitHub.

**Collaborator Tools**

1. Review a pull request Use the “Files changed” tab to comment on code and approve changes.
2. Assign issues or pull requests Use the Assignees menu on GitHub to assign work to collaborators.

**Advanced Commands**

**Undoing Changes**

Unstage a file

~~~
git reset <file>
~~~

Undo the last commit (without losing changes)

~~~
git reset --soft HEAD~1
~~~

Discard local changes to a file

~~~
git checkout -- <file>
~~~

**Stashing Changes**

Save changes for later

~~~
git stash
~~~

Apply stashed changes

~~~
git stash apply
~~~

List stashes

~~~
git stash list
~~~

**Best Practices**

1. Write meaningful commit messages

  Use the imperative mood (e.g., “Add new feature” instead of “Added new feature”).
  Include the “why” of the change, not just the “what.”

2. Keep branches small and focused

  Avoid large, monolithic branches.

3. Review code regularly

  Use pull requests for thorough reviews and feedback.

4. Stay in sync

  Frequently pull changes from the main branch to avoid conflicts.

With these tips and commands, you’ll be navigating GitHub like a pro. Happy coding!
