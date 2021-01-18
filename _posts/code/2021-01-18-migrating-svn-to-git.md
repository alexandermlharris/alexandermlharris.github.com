---
layout: post
category : development
tags : [git, tips, svn]
---

{% include JB/setup %}

Had some old SVN repositories that we needed to migrate to git. The server which hosted SVN was being retired but there was some "current" code still active.

I had used `git svn import` before with some success to migrate simple repos, but found it to be horribly slow. I also had a number of repos that either weren't in the usual format, or were but needed restructuring.

Mostly there were MonoRepo projects which needed splitting out. In some places there were multiple project roots in the same MonoRepo which needed combining into a single project (for example, Red Gate SQL Source control scripts for a database + the app code that went along with it).

Googling found lots of topics, but this caught my attention:

https://www.reddit.com/r/git/comments/99qwxl/migrating_a_subversion_repo_with_multiple/

> This is reasonably straightforward, presuming you are ok with doing a "big-bang" switch from SVN to git. I'd recommend something similar to what u/mindsocket is recommending, but with slightly different tools:
> 
> Use Subgit to convert each svn project into 4 different git repos. You'll essentially do a separate svn->git import for each project. Subgit is a commercial product, but has a free 7-day license for one-time imports. git-svn-clone may also work, but I know that Subgit can handle the variable repo layouts that you describe above.
> 
> Use git-subtree to merge each project, including commit history, into a single repo. To do this, create a fifth git, monorepo, and then use "git subtree merge" to merge each project git into the monorepo.

# SubGit for SVN Export

I looked into [SubGit](https://subgit.com/) and found it is free for one-way SVN exports. While ultimately in my use case I didn't do any more complex configuration that was possible with `git svn` I did find it to be:

* Easy to work with
* MUCH faster to export a repo.
* Seemed more reliable about extracting branches and tags.

For simple cases, I was now done. To finish up and complete the picture, soemthing like this to get the code up to our new shared hosting:

~~~bash
git remote add origin https://my.new.repo.location
git push -u origin --all
git push --tags
~~~

# Merging repos

For more complex cases, I now needed to look at merging together some repos that had been exported seperately. This SO question was good

https://stackoverflow.com/questions/1425892/how-do-you-merge-two-git-repositories and the highest voted answer (not the accepted answer!) really got me over the line:
https://stackoverflow.com/a/10548919/15667

~~~bash
cd path/to/project-b
git remote add project-a /path/to/project-a
git fetch project-a --tags
git merge --allow-unrelated-histories project-a/master # or whichever branch you want to merge
git remote remove project-a
~~~

# Moving a repo to a sub-folder

Same answer also had the solution where I needed to move an entire repo history into a sub-folder:

> In case you want to put project-a into a subdirectory, you can use git-filter-repo (filter-branch is discouraged). Run the following commands before the commands above:

~~~bash
cd path/to/project-a
git filter-repo --to-subdirectory-filter project-a
~~~

And there was a great example of where this had been done on a real, public facing git repo:

https://gist.github.com/x-yuri/9890ab1079cf4357d6f269d073fd9731

# Git Filter Repo
https://htmlpreview.github.io/?https://github.com/newren/git-filter-repo/blob/docs/html/git-filter-repo.html

This took a little bit of getting to work on windows, but I followed the insctructions in the readme and got it working. Basic steps were

1. Copy the git-filter-repo script into the git exec directory
2. Make sure python was installed
3. Fix the name of the python executable as per the suggestion
4. Make sure the python exe was in my path (it wasn't by default) - I did it temporarily as follows:

https://stackoverflow.com/questions/37663602/powershell-add-to-path-temporarily

$env:Path += ";C:\New directory 1;C:\New directory 2"


# More resources

https://subgit.com/documentation/import-book.html#trunk
