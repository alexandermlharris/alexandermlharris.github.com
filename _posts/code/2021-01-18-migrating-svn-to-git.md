---
layout: post
category : development
tags : [git, tips]
---

{% include JB/setup %}


https://www.reddit.com/r/git/comments/99qwxl/migrating_a_subversion_repo_with_multiple/

This is reasonably straightforward, presuming you are ok with doing a "big-bang" switch from SVN to git. I'd recommend something similar to what u/mindsocket is recommending, but with slightly different tools:

Use Subgit to convert each svn project into 4 different git repos. You'll essentially do a separate svn->git import for each project. Subgit is a commercial product, but has a free 7-day license for one-time imports. git-svn-clone may also work, but I know that Subgit can handle the variable repo layouts that you describe above.

Use git-subtree to merge each project, including commit history, into a single repo. To do this, create a fifth git, monorepo, and then use "git subtree merge" to merge each project git into the monorepo.


https://subgit.com/

https://stackoverflow.com/questions/1425892/how-do-you-merge-two-git-repositories
https://stackoverflow.com/a/10548919/15667

~~~bash
	cd path/to/project-b
	git remote add project-a /path/to/project-a
	git fetch project-a --tags
	git merge --allow-unrelated-histories project-a/master # or whichever branch you want to merge
	git remote remove project-a
~~~

In case you want to put project-a into a subdirectory, you can use git-filter-repo (filter-branch is discouraged). Run the following commands before the commands above:

	cd path/to/project-a
	git filter-repo --to-subdirectory-filter project-a
	
https://gist.github.com/x-yuri/9890ab1079cf4357d6f269d073fd9731
https://htmlpreview.github.io/?https://github.com/newren/git-filter-repo/blob/docs/html/git-filter-repo.html


https://stackoverflow.com/questions/37663602/powershell-add-to-path-temporarily

$env:Path += ";C:\New directory 1;C:\New directory 2"


https://subgit.com/documentation/import-book.html#trunk
