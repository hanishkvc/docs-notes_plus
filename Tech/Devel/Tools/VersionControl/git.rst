
Git
====

System Crashes and Git
------------------------

If the system crashes, shortly after a commit, there is 99%
probability many parts of the current branch is lost.

So also the contents of files, if you did a checkout or so,
as the files will get written fresh.



Examples
==========

git init

git config --global user.email "you@example.com"
git config --global user.name "Your Name"
git config --global core.editor vim


git add path/
git add path/file

git commit
git commit -a
git commit -a --amend
git commit --amend
git commit --amend --date=YYYY/MM/DDTHH:MM:SS

git branch
git branch --all
git branch --remotes 
git branch branchName
git branch --delete branchName
git branch -D branchName
git branch --move oldName newName

git checkout backup 
git checkout master 

git reflog
git checkout -b deletedBranch shaOfLastCommitOfThatBranch

git diff
git diff commitId
git diff --cached 
git diff stash
git diff branchName

git log
git log branchName
git log commitId
git log -p
git log --pretty=fuller 

git rebase master 
git rebase --interactive [branchName|commitId_to_start_from]

        Change the rebase/edit-todo contents as required

                edit - to review and edit the commit after it is applied
                break - to break before applying the commit


get rebase originalBaseBranch

	If the base branch from which you branched the current branch, as got updated.
	THis is bit more aggressive at merging compared to just calling merge.

git rebase --committer-date-is-author-date commit_sha_to_start_from



git clear
git fsck
git prune

git repair 
git repair --force

git reset 
git reset --hard 

git revert commitId
git revert HEAD 
git revert --help

git stash branch stash2BranchName
git stash list
git stash show 
git status

git switch -

git tag
git tag tagName

git cherry-pick ASpecificCommitId

git log --pretty=fuller -p




less .git/config 
history | grep -i git | cut -d ' ' -f 3- | sort | uniq
