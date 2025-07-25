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
