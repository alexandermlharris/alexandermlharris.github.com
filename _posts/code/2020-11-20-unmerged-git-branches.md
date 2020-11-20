---
layout: post
category : development
tags : [git, tips]
---

{% include JB/setup %}

I wanted to figure out which feature branches were still to be merged into develop on git, and found the following from https://stackoverflow.com/a/12276041/15667:

~~~bash
git branch -r --no-merged origin/develop

origin/experimental/configtest
origin/feat/XXX-XXXX-feature-one
origin/feat/XXX-XXXX-feature-two
origin/release/4.6
~~~

Ignoring my "experimenal" branch, I can see that
* Feature 1 & Feature 2 are still work in progress and not in develop.
* Release 4.6 is open and hasn't been merged back yet.

If I want to get more info, I found the following, from: https://gist.github.com/l15n/3103708#gistcomment-3273142

~~~bash
for branch in `git branch -r --no-merged origin/develop`;do echo -e "`git show --name-only --format="%>(25)%an | %<(25)%ar" $branch | head -n 1`" $branch; done | sort -r

                      Bob | 85 minutes ago            origin/feat/XXXX-feature-one
                  Charlie | 1 year, 4 months ago      origin/experimental/configtest
                   Alice  | 7 hours ago               origin/feat/XXXX-feature-two
~~~

Which gives me info for who made the last commit to each.
