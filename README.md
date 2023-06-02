texcollab
=========

[note by JA: the `texcollab` script was written in 2015 by graduate student 
Barry Moore II (user `barrymoo` in the examples). I created this fork after Barry graduated. Much of the text that follows is Barry's, but I made some changes over the years and applied
updates and fixes to the code.]

`texcollab` is a shell script that wraps around `git` and `rsync` and is
primarily intended for collaborative work on complex `LaTeX` documents. The original 
developer (Barry) referred to this as his 
 "Advisor-Student/s Mergeless" model. 


`texcollab` is intended to be used in a `bash` shell on a UNIX-type system (Linux, the Linux subsystem
in Windows, or the command line interface on a Mac). Prerequisites are `git`,
`openssh`, `rsync`, and to work on the resulting documents you'll obviously also need
a working  `LaTeX` installation (we use `texlive`). For the comparison of `tex` files modified by different users, the `meld` tool is highly recommended (http://meldmerge.org/) and assumed to be available on the machine where you run `texcollab`. There is also 'Beyond Compare' (https://www.scootersoftware.com/).  Finally, the people collaborating on a project need to have 
accounts on a machine that is remotely accessible by `ssh`, and the accounts should belong to the same user group. In our case, the repositories are typically stored at the local computing
center or on a shared computer in the laboratory.

Because `texcollab` wraps `git` (and `rsync`) commands, most of the
output you see is from `git`. This is sometimes useful, especially when there are errors.

# Why does this exist?


At some point, instead of just emailing each other updates of `tex` files and figures
for a joint manuscript, we started to use `git` for version control of `LaTeX` projects
and also as a way to preserve its history. Problems
became immediately obvious. 
An automated `git merge` works fine for well-structured source code, but `LaTeX` is not quite like
source code. For example, different people use editors with different indentation preferences or end-of-line characters, which for us occasionally caused an automated `git merge` to produce an incomprehensible mess.
Also, a research manuscript tends to come with a large set of figures, usually in some
binary format or with embedded figures in a compressed bitmap format, or with accompanying
Office documents and such. Those files are rarely suitable for version control, and if a set
of PDF figures, say, gets recreated repeatedly, the `git` revisions tree would quickly 
grow too big with no apparent benefit. Our group attempted to switch to a `git` workflow previously, but
found it to be too difficult for beginners. 

Therefore, we came up with a workflow in which files are exchanged via a remote repository, accessible
by all participants in the project, such that some files are under `git` version control whereas
other types of files (binary files, in particular) are exchanged via the same repository using `rsync`. The `texcollab` script combines everything in a single command-line interface. 

`texcollab` also hides much of the `git` and `rsync` syntax and can therefore be used by someone who
is unfamiliar with either. However, it is helpful to have a general idea of how version control works, and
we use the related terms 'commit', 'pull', 'push', 'branch', etc. `texcollab` makes to particular use of the distributed development framework 
offered by `git`; rather, we use it in a more old-fashioned way that some (looking at you, LT) have
referred to as 'ugly and stupid'. Finally, `texcollab` is not foolproof, and sometimes it happens
that someone needs to fix things manually with `git`. 


# What is the "Advisor-Student/s Mergeless" model?

As mentioned, `git merge` for LaTeX is not without disadvantages, and our group needed a simple
workflow for everyone involved with a project. So, the model consists of the
`master` branch used by the senior author and a branch for each "student". The examples that follow
use the branches `master` for the advisor and `barrymoo` for the student or postdoc co-author. The student is
not allowed to commit to `master` and the advisor is not allowed to commit 
to the student branch. Neither the student nor the advisor will ever use an automated `git` merge. 

Instead, `texcollab` makes a distinction between version-controlled files (primarily `tex` files) vs. other data that should not be under version control. `texcollab` provides commands to list version-controlled files that are different in the two branches, and an option to compare those files with the `meld` tool (alternatives to `meld` might exist, but we haven't tried them) and merge the differences in `meld`. This way, there is always someone looking at the pieces that will get merged, and the aforementioned 'incomprehensible mess' is typically avoided. `meld` also has an option to merge all changes from a file in one branch to the same file
in another branch, which is sometimes useful when there are a lot of edits by someone who can be trusted.

An occasional 'manual' intervention with `git` may become necessary if there are many complex updates
in a repository, often with repeated file name changes in one branch vs. another. In this case, an experienced `git` user may have to work on this. Worst case: re-create a new repository from the
files in one of the branches. However, 
this should not be necessary. 

# Directory Structure and Explanation

```
├── .texcollab
├── citations.bib
├── data/
├── esub/
├── figures/
├── main.tex
├── plots/
├── schemes/
├── share/
├── spreadsheets/
└── supporting-information.tex
```

Files/Directories which are tracked:
- `citations.bib`: The citations in bibtex format (not necessary, we use an
  internal citation git repository), name can be changed
- `main.tex`: The main tex files, name can be changed
- `plots/`: A directory where users can create and modify plotting files. Read
  note below!
- `supporting-information.tex`: The supporting information (not necessary), do
  not change name (for `texcollab compile` to work)

Files/Directories which are NOT tracked:
- `.texcollab`: The texcollab configuration file
- `data/`: Contains raw output files from programs
- `esub/`: We use scripts which generate electronic submissions to online
  journals in this directory
- `figures/`: Contains image files used for publication
- `schemes/`: See section 2.5.5 of [ACS Author
  Guide](http://pubs.acs.org/paragonplus/submission/joceah/joceah_authguide.pdf),
we choose to separate these images from `figures/` but you can choose what's
best for you.
- `share/`: Contains binary files generated from specific programs, for example
  ChemDraw or MarvinSketch
- `spreadsheets/`: Contains spreadsheet files, for example from Excel or
  Gnumeric

Important notes about using `plots/`. Please be very careful with this
directory! `texcollab status` is your friend.  I expect people to have the
following types of files in this directory:
- `*.dat`: small parsed data files, likely generated from the `data/`
  directory, to generate images.
- `*.plt`: gnuplot files to generate `*.{eps,pdf}`
- `*.py`: python scripts to generate `*.{eps,pdf}`
- `*.tex`: panels to combine `*.{eps,pdf}` into other `*.{eps,pdf}`

I like to use Inkscape to combine images, but `*.svg` files are ignored. Put
the `*.svg` files into `share/` if you want to share them. 

# SSH Access and Config

We assume you have ssh access to a private server to store the git remote
repository. Additionally, you should set up password-less login to that server.
Finally, we suggest to use `.ssh/config` to ease the process.  Googling "ssh
config" yields [Simplify Your Life With an SSH Config
File](http://nerderati.com/2011/03/17/simplify-your-life-with-an-ssh-config-file/).
The link should be enough to get you started.

# How does tyhe student/postdoc use this model?

It is easy. We assume that the repository is created by one of the co-authors
of the manuscript (student).

First, you need to set up some configuration variables inside `.texcollab` (see
the one in this repo as an example). My group uses this tool for publications
which means a public github really isn't a great idea. We have storage on a
remote machine with `ssh` access, that domain is in `TEXCOLLAB_REMOTE_DOMAIN`
(could be `example.somewhere.com`, or a shortcut in `.ssh/config`).  On the
remote machine exists a directory where  "in-progress" publications are stored,
something like `/$SOME_PATH/shared/latex/barrymoo/$PROJECT_NAME`, which is set
as `TEXCOLLAB_REMOTE_DIR` (obviously all but the `$PROJECT_NAME` should already
exist on the remote machine). The project name should be unique and will be
stored on the remote machine as `$PROJECT_NAME.git` (a standard convention for
git remote repos). Next, you should set your advisor and your user name for
`TEXCOLLAB_ADVISOR/TEXCOLLAB_STUDENT`, respectively. The
`TEXCOLLAB_CURRENT_USER` can be set to `$TEXCOLLAB_ADVISOR` or
`$TEXCOLLAB_STUDENT` (note the `$`) and the `TEXCOLLAB_EDITOR` is used for the
`view` command.

Now you're ready to initialize the directory! Place your `*.tex` file, `*.bib`
files (if necessary), and extras (`figures`, `spreadsheets`, `share`, `data`,
or `schemes`) into the corresponding directories (Always use
`supporting-information.tex` for supporting information). Note: the extras
directories are used to keep backups of various things for the publication
(required for most funding agencies now) and these are ignored by `texcollab`
because they tend to be binary files. Finally, run `texcollab init`, if you
have extras run `texcollab extras push` (additionally), and let the git magic
ensue :) Note, that `texcollab compile` exists and you should probably make
sure it compiles properly before sending it to your advisor (they hate when it
doesn't compile!). 

# How does the advisor use this model?

This is also easy!

Remember, first, that the advisor always uses the master branch. The
student will send him/her the `.texcollab` file. The advisor will navigate to
wherever they want the local copy and run `texcollab clone
$TEXCOLLAB_REMOTE_DOMAIN:$TEXCOLLAB_REMOTE_DIR` (note lack of `.git` ending), replacing
the variable names with the relevant strings from the .texcollab file.

Once the clone is complete, the advisor moves the `.texcollab` file to the
new local git repository (`cd` there) and modify `TEXCOLLAB_CURRENT_USER` to
`$TEXCOLLAB_ADVISOR`, and modify other environment settings as they choose. If there are
'extras', run `texcollab extras pull`.

Finally, the advisor needs to run `texcollab branch
$TEXCOLLAB_STUDENT` (again, enter student manually) to make the student branch
visible (git doesn't do this automatically), and compile the `tex` files. 


# Workflow

Again, this is easy.

Both the student and advisor can make changes in their branches as they see fit, `commit`, `push`,
`pull`, `extras push/pull`, etc. When the advisor, or student, are ready to
merge changes, they run `texcollab compare main.tex <other branch>` ( compares `main.tex` of
the branch you are working on with `main.tex` in the other branch  (student branch `barrymoo`, for example). This will open up the
`TEXCOLLAB_MERGE_TOOL` (`meld` or alternatives) and
then one can pick and choose the changes. Finally, `commit` and then `push`
and the other collaborator is ready to pull. We also added a `texcollab log`
functionality, which means you can use the config key to compare with previous
commits too (see `-h/--help`). 

Before committing any changes with `texcollab commit`, *always* run `texcollab status` to see which files 
are known to `git` and have changed, or which files are not yet known to `git` but not ignored via the settings in `.gitignore`. New/unknown files will all be committed to the revisions tree when you run `texcollab commit`. This includes, unfortunately, any temporary files created while a file is open in an editor, or 
Word files and such that are not placed in one of the 'extras' directories (see above). Therefore, 
check the output of `texcollab status` carefully before you commit. 

*WARNING*: The `texcollab extras push/pull` commands use `rsync`, as mentioned, and the options are set such that 
files will be deleted or replaced without any warnings. In our experience, therefore, it is safest
if only one of the authors pushes and updates extras, and the other authors only pull extras. Of course, 
there may be exceptions to this rule, but they should be communicated among the authors prior to
changing, temporarily or permanently, who pulls and who pushed extras. 


# How to add additional collaborators?

Pass the collaborator the `.texcollab`, have them change the
`TEXCOLLAB_STUDENT` variable, clone the repository as the advisor does, and
`texcollab add-collab $TEXCOLLAB_STUDENT` (the `$TEXCOLLAB_STUDENT` is
obviously the new collaborator). The collaborator can now edit/commit/push/pull
as normal now. AND, more importantly (arguably), the advisor can pull and
`texcollab branch $NEW_STUDENT_BRANCH` to merge changes from the new
collaborator. Presumably, after something like this happens, other co-authors will 
be notified that they need to merge changes from the advisor's branch.

# `view` and `compare`, what's a revision?

In the `-h/--help` string, you may see that `view` and `compare` work with
revisions, but what is a revision? A revision is basically a previous commit's
version of the file of interest. The best way to get this information is to run
`texcollab log`, for example:

```
commit 45f158b34cef9141aaeacebc09f37ff800071132
Author: Jon Doe
Date: Tue Jun 2 11:54:07 2015

    Initial Commit
```

The revision is: `45f158b34cef9141aaeacebc09f37ff800071132`, copy and paste the
string to `compare` and `view` to use the revision.

# Anything else?

This is a work in progress, but our group is publishing in a variety of scientific journals using this tool. We have even used it for a book (https://ja01.chem.buffalo.edu/in-focus-mo-ebook/in-focus-mo-ebook.html). Email us if you have suggestions or come across a bug.

# Design Principles, Tips, FAQ, Etc.

1. Important Design Principle: Nothing that isn't tracked by `texcollab` should
   be edited inside a `texcollab` directory. What does that mean? Your content in
   `extras` should be edited somewhere else. For example, if you have the following directories:
    - `~/projects/$PROJECT_NAME`
    - `~/publications/$PROJECT_NAME` </ul>
 then edit `extras` in `projects` and copy to `publications`. As mentioned, files in `extras`
    are at risk of being replaced or deleted. Keeping separate copies of the files
    in `extras` will help to mitigate this risk.
2. ALWAYS, ALWAYS, ALWAYS run `texcollab status` before `commit/push`, this
   prevents you from committing, potentially huge, files you did not intend to. 
   Such files should either be listed in `.gitignore` or stored in one of the `extras`
   directories. 

# Advanced Settings for `.texcollab`

This section exists in case you don't have `sudo` access on the remote server.
If the standard `.texcollab` template doesn't work for you, you may consider adding these
settings:

1. `TEXCOLLAB_REMOTE_RSYNC`: Allows you to use non-standard remote rsync
2. `TEXCOLLAB_GROUP`: Allows you to change group ownership on remote
