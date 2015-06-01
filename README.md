texcollab
=========

A LaTeX/Git wrapper for the "Advisor-Student/s Mergeless" model written entirely in shell script.

# Why does this exist?

`git merge` works perfectly fine for source code, but LaTeX is not quite like source code. Our group attempted
to switch to a `git` workflow previously, but found it to be too difficult for beginners. I decided to step in
and fix the problem by simplifying and developing a standard collaboration workflow.

# What the heck is the "Advisor-Student/s Mergeless" model?

As I said, I dislike `git merge` for LaTeX and I need a simple workflow for new students. So, the model consists
of the `master` branch (advisor) and a branch for each "student" (I will refer to myself as the student i.e. the
`barrymoo` branch). The student is never allowed to commit to `master` and my advisor is never allowed to commit
to `barrymoo`. Neither the student nor the advisor will ever `git merge`.

# How can I use this model?

It's pretty easy, I promise. First, you need to set up some configuration variables inside `.texcollab` (see
the one in this repo as an example). We are using this tool for publications which means a public github really
isn't a great idea. We have a storage server with `ssh` access, the domain goes in `TEXCOLLAB_REMOTE_DOMAIN`.
On the remote machine exists a place where I put my "in-progress" publications, something like
`/$SOME_PATH/shared/latex/barrymoo/$PROJECT_NAME`, which I set as `TEXCOLLAB_REMOTE_DIR` (obviously all but the
`$PROJECT_NAME` should already exist!). The project name should be unique and will be stored on the remote machine
as `$PROJECT_NAME.git` (a standard style for git repos). Finally, you should set your advisors and your name for 
`TEXCOLLAB_ADVISOR/TEXCOLLAB_STUDENT`, respectively. Now place your `*.tex` file, `*.bib` files (if necessary),
figures to the `figures` directory (Always use `supporting-information.tex` for supporting information). Run
`texcollab init`, if you have figures run `texcollab figures push` (additionally), and let the git magic ensue :) 

# How does my advisor use this model?

Also easy! Send him/her the `.texcollab` file. They navigate to wherever they want the local copy and run
`texcollab clone $TEXCOLLAB_REMOTE_DOMAIN:$TEXCOLLAB_REMOTE_DIR` (note I don't add `.git` ending!), if you have
figures run `texcollab figures pull`, and then they need to run `texcollab branch $TEXCOLLAB_STUDENT` to make the
student branch visible, and voila!

# What do I do now, how do we share commits?

Again, easy :P I hope you see the pattern now. Both the student and advisor can make changes willy-nilly! Commit,
push, pull, figures push/pull, etc. When the advisor, or student, are ready to pull in the other ones changes
run `texcollab compare something.tex`, this will open up the `TEXCOLLAB_MERGE_TOOL` (we like meld) and then
pick and choose the changes you want (or don't!). Finally, commit/push and the other collaborator is ready to go.

# Cool, how do I add collaborators?

I won't say it this time. Pass the collaborator the `.texcollab`, change the `TEXCOLLAB_STUDENT` variable, clone
the repository as the advisor does, and `texcollab checkout $USER` (the $USER should be equal to `TEXCOLLAB_STUDENT`
point). Now, the advisor can pull and `texcollab branch $new_student_branch` to `meld` with both students changes.

# Anything else?

This is work in progess and I haven't done much testing as of yet. Please send me issues, or suggestions, here
on github. I am very interested in making a nice collaboration tool!
