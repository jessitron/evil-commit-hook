# Git Commit Hooks

TODO: make a project on github that copies an obnoxious hook in on npm setup
TODO: make its last commit be, "lots of stuff that other team members did"

<!-- shot(1) git clone; npm setup -->

The other day, I cloned another team's project and followed the setup instructions.
I'm trying something out, maybe a refactor, first make a quick commit --

<!-- shot(2) try to make a commit. The script does a bunch of annoying crap -->

What. Is. This?

Why is it running all this slow stuff? This is a local commit. It should be instantaneous.

<!-- shot(3) after a while the damn thing rejects my commit -->

What?? it rejected my commit?

<!-- shot(4) git log -->

not OK! What is causing this?

I bet it's a git hook, messing with commit behavior.

<!-- shot(5) google git hooks. click on https://git-scm.com/docs/githooks -->

What are git hooks?

<!-- shot(6) highlight -->

Hooks are programs to trigger actions at certain points. Like when I want to make a commit.

<!-- shot(7) highlight -->

Where is this evil program... hmm, it's either in GIT_DIR/hooks or this core.hooksPath config.

<!-- shot(8) -->

`git config --get core.hooksPath`

Check the config first. Nothing. It is not defined.

<!-- shot(9) cd .git/hooks -->

Look in the default location. the GIT_DIR is .git, and under that is hooks

<!-- shot(10) ls -->

here's a bunch of stuff. Most of it ends in 'sample', those do nothing. Ah-ha, here's one that might be executable.

<!-- shot(11) open pre-commit -->

Indeed! Here's all that evil stuff!
the "exit 1" here tells git "Don't continue with the commit."

This is not a realistic example, the ones I see in real life are more abstract and not so easy to read.

<!-- shot(12) edit -->

`echo "Jess was here"`

I can change something in here and run it again.

<!-- shot(13) try the commit again -->

There's my message. Now I'm sure that's the right place.

How did that script get there? Not in the clone, the .git directory isn't copied.

<!-- shot(14) package.json -->

but something in the project setup copied that script in there.
Again, this is unrealistically straightforward, more likely it's like a postinstall of some npm module,
good luck figuring it out.

I could delete that hook script, but something might put it back.
Instead, here's a quick workaround:

<!-- shot(15) -->

`git commit -m wip --no-verify`

The no-verify argument tells git to skip all the hooks. Just do the fricking commit.

OK. Now that you know how annoying git hooks can be, let's use them for good.

<!-- shot(16) cd to another project -->

The other day, Avdi got frustrated because he kept making commits to the main branch in a big shared repo.

<!-- shot(17) -->

`git commit -m "Oops, I'm gonna regret this commit"`

He always wants to commit to a branch of his own.

I can save him some frustration!

<!-- shot(18)  -->

`code .git/hooks/pre-commit`

Open a pre-commit script.

<!-- shot(19) -->

`echo "YOU'RE ON MAIN AGAIN, AVDI"`

First, say hello to make sure we're in the right place.

<!-- shot(20) -->

`exit 1`

and I'll have it exit 1 so I can test it without making a bunch of test commits, handy.

<!-- shot(21) terminal -->

Try that commit again, and sure enough it's running our script.

<!-- shot(22) -->

This is the right thing to do on the main branch, but not on another branch.

```
git switch -c avdi
git commit -m "This should work"
```

Let's change that behavior.

<!-- shot(23) google for git current branch name -->

What branch are we on? I google this every time I need it.

<!-- shot(24) -->

`git rev-parse --abbrev-ref HEAD`

Obviously.

<!-- shot(25) pre-commit -->

```
current_branch=$(git rev-parse --abbrev-ref HEAD)
echo "Current branch is: " $current_branch
```

Stick that in a variable in our script. Print it out to test

<!-- shot(26) try the commit again -->

OK! It knows we're on the avdi branch

<!-- shot(27) pre-commit -->

`if [ $current_branch = "main" ]`

Now put that in a conditional

<!-- shot(28) try the commit -->

This time the commit works!

<!-- shot(29) -->

```
git switch main
git commit -m "This should fail"
```

Try it on main, and yep, it fails!

<!-- shot(30) precommit -->

Remove this comment.

<!-- shot(31) one more try -->

```
git switch avdi
git commit -m "This should work
git switch main
git commit -m "This should fail"
```

now Avdi's frustration will be reduced.

This is a GOOD use of pre-commit hooks, because it's personal. Use them to customize your own environment.

If you want to run linting and tests, that's what Continuous Integration is for.

So there.
