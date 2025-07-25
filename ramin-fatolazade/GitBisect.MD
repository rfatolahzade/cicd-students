# git-bisect
Use binary search to find the commit that introduced a bug
```bash
git bisect <subcommand> <options>
```

### DESCRIPTION
The command takes various subcommands, and different options depending on the subcommand:
```bash
git bisect start [--term-(bad|new)=<term-new> --term-(good|old)=<term-old>]
	  [--no-checkout] [--first-parent] [<bad> [<good>...]] [--] [<pathspec>...]
git bisect (bad|new|<term-new>) [<rev>]
git bisect (good|old|<term-old>) [<rev>...]
git bisect terms [--term-(good|old) | --term-(bad|new)]
git bisect skip [(<rev>|<range>)...]
git bisect reset [<commit>]
git bisect (visualize|view)
git bisect replay <logfile>
git bisect log
git bisect run <cmd> [<arg>...]
git bisect help
```
This command uses a binary search algorithm to find which commit in your **project’s history introduced a bug**.
You use it by first telling it a "bad" commit that is known to contain the **bug**, and a "good" commit that is known to be **before** the bug was introduced.
Then git bisect picks a commit** between those two endpoints** and asks you whether the selected commit is "good" or "bad". It continues narrowing **down** the range until it **finds the exact commit that introduced the change**.


### Real-Word Senario!!
In this section, I'm going to show how bisect works.

I've already added a "Real-World Scenario!!" section in the GitBisect.MD file (Just title).
Everything is OK so far. Also, I'll commit this added note in the next commit.
```bash 
ram@NL:~/cicd-students/ramin-fatolazade$ git log -2 --oneline --graph
* f6f841a (HEAD -> dev, origin/dev) A real-world Scenario!!
* f4d971c Add GitBisect.MD
```

My log:
```bash
ram@NL:~/cicd-students/ramin-fatolazade$ git log -3 --oneline --graph
* c5e6203 (HEAD -> dev, origin/dev) A pre description of Scenario!!
* f6f841a A real-world Scenario!!
* f4d971c Add GitBisect.MD
```

Now, I’ll intentionally add a typo (misspelled word/syntax error and bug file) in the next commit to simulate a bug!

Updated log:
```bash
ram@NL:~/cicd-students$ git log -4 --oneline --graph
* fb2c68d (HEAD -> dev, origin/dev) A simulate scenario desc
* c5e6203 A pre description of Scenario!!
* f6f841a A real-world Scenario!!
* f4d971c Add GitBisect.MD
```

I'm going to change line 28 to "### Real-Word Senario!!" and add a bug file in the working directory (and some commits).
```bash
ram@NL:~/cicd-students/ramin-fatolazade$ git log -8 --oneline --graph
* c4c5a95 (HEAD -> dev, origin/dev) A commit 2
* 779ae51 A commit 1
* c3e6f53 A ordinary commit with message
* fb2c68d A simulate scenario desc
* c5e6203 A pre description of Scenario!!
* f6f841a A real-world Scenario!!
* f4d971c Add GitBisect.MD
* d01f9d0 M dir name
```
On commit "c4c5a95" we find out the bug and typo!
lets use bisect to find and revert to good commit
for start we should select commits and check between when everything was ok and bad
```bash
ram@NL:~/cicd-students/ramin-fatolazade$ git log -8 --oneline --graph
* c4c5a95 (HEAD -> dev, origin/dev) A commit 2
* 779ae51 A commit 1
* c3e6f53 A ordinary commit with message
* fb2c68d A simulate scenario desc
* c5e6203 A pre description of Scenario!!
* f6f841a A real-world Scenario!!
* f4d971c Add GitBisect.MD
* d01f9d0 M dir name
ram@NL:~/cicd-students/ramin-fatolazade$ git bisect start
status: waiting for both good and bad commits
ram@NL:~/cicd-students/ramin-fatolazade$ git bisect bad
status: waiting for good commit(s), bad commit known
ram@NL:~/cicd-students/ramin-fatolazade$ git bisect good c5e6203
Bisecting: 1 revision left to test after this (roughly 1 step)
[c3e6f5306f7bf5ab512a05f40a4d71f4db51ffa0] A ordinary commit with message
ram@NL:~/cicd-students/ramin-fatolazade$ git bisect good fb2c68d
Bisecting: 0 revisions left to test after this (roughly 1 step)
[779ae518aae2bd0edd0eff2d2ff808bd1750e8b1] A commit 1
ram@NL:~/cicd-students/ramin-fatolazade$ git bisect bad c3e6f53
c3e6f5306f7bf5ab512a05f40a4d71f4db51ffa0 is the first bad commit
commit c3e6f5306f7bf5ab512a05f40a4d71f4db51ffa0
Author: rfatolahzade <14954967+rfatolahzade@users.noreply.github.com>
Date:   Fri Jul 25 10:37:41 2025 +0330

    A ordinary commit with message

 ramin-fatolazade/GitBisect.MD | 13 ++++++++++++-
 ramin-fatolazade/bug          |  1 +
 2 files changed, 13 insertions(+), 1 deletion(-)
 create mode 100644 ramin-fatolazade/bug

ram@NL:~/cicd-students/ramin-fatolazade$ git show c3e6f5306f7bf5ab512a05f40a4d71f4db51ffa0
commit c3e6f5306f7bf5ab512a05f40a4d71f4db51ffa0
Author: rfatolahzade <14954967+rfatolahzade@users.noreply.github.com>
Date:   Fri Jul 25 10:37:41 2025 +0330
.....
-### Real-World Scenario!!
+### Real-Word Senario!!
....
+++ b/ramin-fatolazade/bug
@@ -0,0 +1 @@
+𖢥
```
