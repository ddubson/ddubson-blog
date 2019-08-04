---
title: "Save with WIP: a git technique in Trunk-Based Development"
description: "hello"
date: 2019-08-04T10:21:07-04:00
draft: false
---

When building a software product on a team, to create an effective and sustainable development flow, one needs to abide 
by a version control branching model that is agreed upon all team members. If there is a chosen approach to branching, 
individual contributors on a team can contribute code without issue.

In this post, I want to focus on a specific technique within a specific branching model that I believe is quite 
effective at keeping each contributor productive without introducing cognitive overhead of a complex branching model.

The branching model being [Trunk-Based Development](https://trunkbaseddevelopment.com/) and the technique that I'll 
refer to as **"Save with WIP"**, where WIP stands for Work-In-Progress.

Trunk-Based Development model encourages merging to the 'trunk' (think of a tree trunk that all branches spring from) 
as often as possible and keeping branches short-lived: no more than a day or so if possible. We claim 'master' branch 
as the trunk.

> Read more about the benefits of [Trunk Based Development](https://trunkbaseddevelopment.com/)

To that effect, the technique **"Save with WIP"** encourages small save commits that are temporary and will never make 
it to the trunk, i.e. 'master' branch. This technique helps us create checkpoints at some predetermined stopping points 
in your development flow. I think of cases when I have to leave for the day or go to a meeting at a not-so-clean point 
in my feature story.

Here's how it goes.

## Save with WIP

Clone the git repository as the first step if you don't already have it on your machine. I'm using a fake git 
repository to drive out this example alongside a phantom product.

{{<highlight bash>}}
git clone https://github.com/ddubson/my-repo.git
{{</highlight>}}

Make sure you are on the 'master' branch (the trunk).

Let's create a simulated example of some git history:

![](/images/trunk-dev-wip-commits/git-hist-1.svg)

Check out a new feature branch off of local 'master' branch

{{<highlight bash>}}
git checkout -b display-item-details-1122337
{{</highlight>}}

The name format of the branch follows **[short-description-of-story]-[story-id]**

Now we can write some code for this feature story...

Once we have a stopping point that isn't the end of the story, we can utilize **Save with WIP** technique

The steps are:

- **Add all changed files to Git staging**
    - WIP commits are temporary, we will come back and review all changes once the story is done. For now, add all the changes.
- **Create a WIP commit with specific format** (depicted below)
    - Prefix the commit message with "WIP: "
    - the commit message format should indicate what is currently being worked on in the branch or which test suites are failing
- **Push to remote feature branch**

{{<highlight bash>}}
git add .
git commit -m "WIP: working on rendering item SKU"
git push origin head
{{</highlight>}}

![](/images/trunk-dev-wip-commits/git-hist-2.svg)

When we are ready to resume working on the story, we can continue contributing new code.

After some time, we might need to create more WIP commits like so:

![](/images/trunk-dev-wip-commits/git-hist-3.svg)

On the left, we can see that the history of origin/master has advanced. This is to represent that 
other developers on the team have merged their code changes to remote master. We will reconcile these
changes below.

Once we are done with the story (we know that we are done after all the test cases for the given feature pass),
we can add the final WIP commit to track the final chunk of the story.

{{<highlight bash>}}
git add .
git commit -m "WIP: finished feature display items"
git push origin head
{{</highlight>}}

![](/images/trunk-dev-wip-commits/git-hist-4.svg)

We can now create the formal feature story commit, here are the steps:

- Reset commits on the feature branch to where master branch is
- Add all new files that are untracked
- Pick through all the changes made in the story
- Create formal commit

{{<highlight bash>}}
# Story is done, and we can reset the WIP commits back to where the original branch veered off of 'master' branch
git reset master

# WIP commits have been erased locally
# Add all the new files added as part of this feature
git add -N .

# Pick through the changes of this feature
git add -p

# Create a formal feature commit
git commit -m"Add display item details feature [#1122337]"
{{</highlight>}}

Here's what our git history looks like now:

![](/images/trunk-dev-wip-commits/git-hist-5.svg)

Note that the git hash has changed, indicating that this is a completely different commit from any of the WIP commits.

We can now switch back to the master branch and merge the changes from the feature branch

{{<highlight bash>}}
git checkout master

git merge display-item-details-1122337

# Check git history
git log --graph --decorate --oneline
{{</highlight>}}

This should be a fast-forward merge so there will not be any merge conflicts.

![](/images/trunk-dev-wip-commits/git-hist-6.svg)

But we do have to reconcile changes with origin/master.

Here's how we can safely do this:

{{<highlight bash>}}
git pull origin master --rebase
{{</highlight>}}

Rebase flag is enabled, and so our feature commit will be placed on top of the commits that came earlier on origin/master

Rebasing concept is beyond the scope of this post, but you can [find more here](https://git-scm.com/book/en/v2/Git-Branching-Rebasing).

After a successful rebase, we can observe similar results as below:

![](/images/trunk-dev-wip-commits/git-hist-7.svg)

Now all that's left is to push to origin

{{<highlight bash>}}
git push origin master
{{</highlight>}}

That's it.

---

There are many variations on how to approach reconciling a feature branch, master, and origin/master. I have 
depicted only one of them, but if there are others that work better for you, do not hesitate to do what is right
for you or your team. 

I have also specifically omitted formal commits on a feature branch that are tiny enough to merge to master
without completing the entire story. This is on purpose so as to not complicate the flow in this happy path example.
I encourage you to add tiny commits that further the story while chipping away at its overall complexity if possible.

## Vocabulary

**Branching** - version control concept of duplicating source code so that modifications can be done in parallel alongside multiple branches. 

**Branching Model** - a defined pattern of branching.

**Trunk-Based Development** - branching model centered around having the central trunk, i.e. 'master' branch as the main branch.

**WIP Commit** - convention for calling a temporary commit that is meant to be a temporary save point during the lifetime of a user story.

**Feature Branch** - convention for calling a git branch that is used specifically to work on one user feature story.

## References

- [Branching (version control)](https://en.wikipedia.org/wiki/Branching_(version_control))
- [Trunk-Based Development](https://trunkbaseddevelopment.com/)
- [Git Guide - Rebasing](https://git-scm.com/book/en/v2/Git-Branching-Rebasing)
