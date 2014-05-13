---
layout: post
title:  "Our Very Simple Git Branching Model"
author: Mauricio Ulloa
date:   2014-03-19 13:00:00
categories: Softwre-Engineering Git Gitflow
---

One day I was reading [Hacker News][hn] when I stopped in a gist named _[a simple git branching model][article]_ by [Juan Batiz-Benet][jbenet]. After sending it to The Amazing Dev Team we started discussing how to have a simple, clean and semantic branching model.

After a few conversations during these months, we decided to write it down to specify the rules and why they work for us.


# The Rules

1. `master` must always be deployable
1. **all changes** should be made using feature branches (merging with `master`)
1. **all hotfixes** should be made in the `hotfixes-{name}`
1. rebase to avoid/resolve conflicts; merge into `master`


# The explanation

1. `master` _must always be deployable_<br />
We deploy `master` when pushing to origin. We do this using Jenkins and Continuous Integration.
1. _**all changes** should be made using feature branches (merging with `master`)_<br />
This way branches have more meaning in a semantic way. Each branch is a feature of the project and the code can easily be found.
1. _**all hotfixes** should be made in the `hotfixes-{name}` branches_<br />
When lauching an application you receive a lot of feedback, specially if you have more than 9 million readers a month. This feedback sometimes is a new feature or sometimes is a quick and hot fix that should be deployed **immediately**. One optimization to this step is creating a hotfixes branch every release and deploy it to production, this way you will keep the master clean and you have a fast response time.
1. _rebase to avoid/resolve conflicts; merge in to `master`_<br />
Please avoid the [merge bubbles][merge-bubles]. Please.


#Some Code To Understand

```bash

# First, create a new branch from master.
$ git pull origin master
$ git checkout -b new-feature

# Make changes and commits.
$ git add .
$ git commit -m "Changes"

# When finishing the new feature you should bring the code inside master to the branch.
# It's a good practice to make it frequently during the development of the feature.
$ git fetch origin master
$ git rebase origin/master
$ git push origin new-feature
# Sometimes, when there are conflits at rebasing, you should make git push -f origin new-feature.

# After finishing, you should merge and push the new features to master.
$ git checkout master
$ git pull origin master
$ git merge --no-ff new-feature
$ git push origin master

```

The importance of `--no-ff` flags when merging is huge, because it prevents a "fast forward" constructing a commit for the merge. You can read more information in this [post][so-no-ff].

# The results

If you follow this methodology you can transform this merge chaos:

![alt text](/images/without-simple-branching.png "Without our 'Very Simple Git Branching Model'")

Into this beautiful git pipeline:

![alt text](/images/with-simple-branching.png "With our 'Very Simple Git Branching Model'")

Feel free to send feedback!

[hn]: https://news.ycombinator.com/
[article]: https://gist.github.com/jbenet/ee6c9ac48068889b0912
[jbenet]: https://gist.github.com/jbenet
[merge-bubles]: https://gist.github.com/jbenet/ee6c9ac48068889b0912#wont-git-merge---no-ff-generate-merge-bubbles
[so-no-ff]: http://stackoverflow.com/questions/9069061/what-is-the-difference-between-git-merge-and-git-merge-no-ff
