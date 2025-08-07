# cherrypick
This guide demonstrates the use of git cherry-pick in a Git workflow. The goal is to show how specific commits can be selectively applied from one branch to another. This is especially useful when you want to bring in a single feature or fix without merging an entire branch.

Step 1: Create the GitCherrypick.md File and Commit 

First, we create a documentation file to track this process and commit it to the repository. 
```bash
ram@NL:~/cicd-students/ramin-fatolazade$ echo "# cherrypick " > GitCherrypick.md
git add GitCherrypick.md
git commit -m "Add cherrypick md file"
```

Step 2: Create a New Branch Called devops
We now create a new branch named devops to simulate a feature development environment.
```bash
ram@NL:~/cicd-students/ramin-fatolazade$ git checkout -b devops
```
Switched to a new branch 'devops' and commit feature-a/b as below:
```bash
ram@NL:~/cicd-students/ramin-fatolazade$ echo "Feature A" > feature-a.txt
git add feature-a.txt
git commit -m "Add Feature A"
echo "Feature B" > feature-b.txt
git add feature-b.txt
git commit -m "Add Feature B"
```

Step 3: Check WD After Commits
After adding both features(a/b), the WD contains the following files:
```bash
ram@NL:~/cicd-students/ramin-fatolazade$ ls
GitBisect.MD  GitCherrypick.md  README.md  bug  feature-a.txt  feature-b.txt
```
Step 4: Switch to the dev Branch
We now checkout to the dev branch to selectively apply only one of the changes(feature a in my case).

```bash
ram@NL:~/cicd-students/ramin-fatolazade$ git checkout dev
Switched to branch 'dev'
Your branch is ahead of 'origin/dev' by 1 commit.
  (use "git push" to publish your local commits)
```
Step 5: View Commit History 
To identify which commit we want to cherry-pick, we inspect the commit history:

```bash
ram@NL:~/cicd-students/ramin-fatolazade$ git log --oneline --graph --all -8
* 426713d (devops) Add Feature B
* 35858b6 Add Feature A
* cfeabc4 (HEAD -> dev) Add cherrypick md file
* c4c5a95 (origin/dev) A commit 2
* 779ae51 A commit 1
* c3e6f53 A ordinary commit with message
* fb2c68d A simulate scenario desc
* c5e6203 A pre description of Scenario!!
```
From the log: 

   - Commit 35858b6 → "Add Feature A"
   - Commit 426713d → "Add Feature B"

Step 6: Current Working Directory on dev
Before cherry-picking, the dev branch does not have the feature files:
```bash
ram@NL:~/cicd-students/ramin-fatolazade$ ls
GitBisect.MD  GitCherrypick.md  README.md  bug
```
Indeed, feature-a.txt and feature-b.txt are missing — they exist only on the devops branch.

Step 7: Perform the Cherry-Pick 
We now use git cherry-pick to apply only the "Add Feature A" commit (35858b6) to the current branch (dev): 

```bash
ram@NL:~/cicd-students/ramin-fatolazade$ git cherry-pick 35858b6
[dev b197f0c] Add Feature A
 Date: Fri Jul 25 23:11:31 2025 +0330
 1 file changed, 1 insertion(+)
 create mode 100644 ramin-fatolazade/feature-a.txt
```

Step 8: Verify the Result 
Now check the WD again: 
```bash
ram@NL:~/cicd-students/ramin-fatolazade$ ls
GitBisect.MD  GitCherrypick.md  README.md  bug  feature-a.txt
```
As you can see, feature-a.txt has been successfully added to the dev branch. However, feature-b.txt is not present
this confirms that only the desired commit was cherry-picked, not the entire devops branch or any other changes. 
Let’s view the updated log:
```bash
ram@NL:~/cicd-students/ramin-fatolazade$ git log --oneline --graph --all -8
* b197f0c (HEAD -> dev) Add Feature A
| * 426713d (devops) Add Feature B
| * 35858b6 Add Feature A
|/
* cfeabc4 Add cherrypick md file
* c4c5a95 (origin/dev) A commit 2
* 779ae51 A commit 1
* c3e6f53 A ordinary commit with message
* fb2c68d A simulate scenario desc
```
A new commit b197f0c appears on dev, applying the same change as 35858b6 and the devops branch still has both commits.
There is no merge of the full devops branch — only one commit has been selected.
