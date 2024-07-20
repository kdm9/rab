# `git-annex`: version control for reproducible analyses

# Introduction

[Git](https://git-scm.com/) has been a revolutionary tool for online
collaborative software development. [Git
annex](https://git-annex.branchable.com/) extends git's abilities to track and
share large files in addition to code. Together these tools enable effective
version control and collaboration on large bioinformatics analyses.

This chapter is a motivating case study on using git-annex to version an
analysis workspace between multiple collaborators. I'm not going to re-hash the
excellent [git-annex
documentation](https://git-annex.branchable.com/walkthrough/) which you should
certainly read, instead I'll show how I have used `git-annex` in my work.

Also, please note that I assume some degree of familiarity with basic `git`
below. Please avail yourself of one of the many excellent git tutorials if you
are new to git or if your git knowledge could use refreshment. Personally,
I recommend [Software Carpentry's git-novice
tutorial](https://swcarpentry.github.io/git-novice/) for complete beginners,
and [Learn Git in X minutes](https://learnxinyminutes.com/docs/git/) for those
who need a quick refresher or reference. And of course, the [git documentation
website](https://git-scm.com/docs/) is the complete documentation of all the
details, and even have their own [git
tutorial](https://git-scm.com/docs/gittutorial).

### Part 1: Why bother

We have been doing computation analyses for as long as there have been
computers, so why bother with all this fanciness? In a nutshell: collaboration.
To analogize somewhat loosely, tools like Google Docs are dramatic improvements
over the traditional method of emailing around a million Word docs named like
`Document_final_v3_revisions_supervisorcomments-v2_final.docx`. Similarly,
`git` (and other version control software before it) made collaborative
software development far easier and more accessible than mailing patches to
some development mailing list.

But why git-annex specifically? Git itself was designed to work with code, but
we wish to track not just our code, but also our raw data and some intermediate
output data. The Linux kernel (for which git was originally developed) is about
30 million lines of code, totalling hundreds of megabytes of source code and
accessory files. Despite being one of the largest and most active free software
projects in the world, even the Linux kernel is dwarfed by nearly any modern
genomics dataset: a single sample from the example *Arabidopsis* project below
is over 1GB of sequence data, and the total project consists some 12TB of raw
data. Git itself cannot handle this volume of data, and so various additions
and extensions to git have been developed. git-annex is to my eyes the most
applicable one to the typical biological data scientist [^1].

How does git-annex differ from git? To a first approximation, it doesn't
really. Git-annex works on top of git, detecting large files and tracking them
as symlink pointers to a hidden data store. One can then separately coordinate
syncing of these large data files, either individually or in aggregate. Aside
from this, git-annex behaves nearly identically to git itself, largely because
it *is* just a wrapper around git for most other operations, as we will see
below.

### Part 2: Worked Example

> I'll be assuming you're already familiar with git itself. If you're not, or
would like a refresher, I suggest either the , or the software carpentry [git
course](https://swcarpentry.github.io/git-novice/).

OK, so how do we actually use it? The git annex workflow is very similar to
that of git. One makes changes, then stages, commits, and pushes them to
a remote. The below commands give a brief outline of a hypothetical workflow.

For context, we are working on a large collaborative population genomics
project in *Arabidopsis*, using the
[Acanthophis](https://github.com/kdm9/Acanthophis) variant calling pipeline. We
work on our laptops, and use a centralised large server and cluster (with
shared file system) to perform the larger analyses. The following steps/tips
aren't necessarily ordered, and are to some extent specific to this analysis,
but hopefully the example I give here is illustrative enough to be useful more
broadly.

#### Initialisation and configuration

First, let's make the git repo. One needs to initialise git annex separately.

```bash
cd project-workspace
git init
git annex init
```

We need to configure which files are handled by git itself, by git annex, or
are ignored by both. Let's say we have a Snakemake workflow at the root of the
repo (like we just covered), and directories containing raw data (which can't
be recreated easily) (`./rawdata`), temporary/computed data in `./tmp`, and
a notebook (`./notebook`) which contains multiple shell, R, and python scripts
with associated data.

```bash
cat > .gitignore <<EOF
tmp
EOF

cat > .gitattributes <<EOF
* annex.largefiles=largerthan=5Mb
*.sh annex.largefiles=nothing
*.R annex.largefiles=nothing
*.py annex.largefiles=nothing
*.txt annex.largefiles=largerthan=10Mb
*.tsv annex.largefiles=largerthan=10Mb
*.csv annex.largefiles=largerthan=10Mb
*.ipynb annex.largefiles=largerthan=10Mb
EOF
```

The above configs cause all files in `./tmp` to be ignored by git altogether.
We then tell `git annex` to track any file larger than 10kb, excluding any
shell, R, or python scripts. One can customise things further if there are
certain files or directories that should always be considered either small
(tracked by *Ye Olde* `git`) or large (tracked by `git annex`) (e.g. adding
`*.Rmd` to the list of small files, or forcing everything under `./rawdata` to
be considered a large file).


#### Committing

Staging, committing, merging, and diffing files or change sets operates
identically to "normal git". Both `git add` and `git annex add` will look up if
files should be "large" (see previous section), and then either commit their
changes traditionally, or commit them as large files. There is one caveat to
that: `git annex add` (but not `git add`) takes a `--force-large` or
`--force-small` argument to override the configs and force adding files as
either large or small (traditional) files. This is useful if you have e.g.
a very large data file that is named `data.txt`, which with the above config
would normally be considered a small file.

```bash
git annex add .
# git add . would have the same result, but would not print any messages about adding large files.
git commit -v
```

#### Pushing (well, syncing)

Git annex behaves most differently when it comes to synchronisation. With
normal git, there is often a simple process of pushing to GitHub, then pulling
from GitHub to a third computer (perhaps a server, or a collaborator's laptop).
Git annex does things a little differently: synchronisation is bidirectional by
default, and there is an additional layer/step to handle large files. There are
also a couple of gotchas, which I highlight in the below code.

```bash
# add a remote
git remote add taco taco:/path/to/repo
```

```bash
# Perform a bidirectional sync. 
git annex sync
```

BEWARE: By default, git annex sync will commit any changes before syncing,
unlike git pull which would normally error and ask you to either stash or
commit them. Please make sure this is OK, or run git stash before git annex
sync [^2].

Also, note `git annex sync` alone did nothing to copy large files. To
automatically synchronise all large files between all remotes, do:

```bash
git annex sync --content
```

However, I normally find it better to chose which large files to sync. In our
*Arabidopsis* example, the `./rawdata` directory has over 12TB of data files
(mostly sample FASTQs). Obviously we don't want that synced to my laptop.
Instead, I want the various smaller computed files (e.g. filtered VCFs of some
tens of GB), which are in my case under `notebook/data/` (yes, this is
a simplification of how I actually work, but it shows the principles). 

To get (from a remote) some large files, use `git annex get`, and to copy some
new large files to a remote, use `git annex copy`. One can specify a host with
`--from` or `--to` as appropriate, or git annex will guess based on where it
knows which hosts files are on. For example:

```bash
git annex get --from taco  ./notebook/data/1_filtered_variants/
```

Say I then ran some analyses on my laptop to set up a popgen modelling analysis
using a subset of this data. If I wanted to run these models on a server,
I could then do (e.g.):

```bash
git annex add notebook/data/2_modelling
git commit -m "add modelling inputs"
git annex sync
git annex copy --to taco notebook/data/2_modelling
```

This would stage and commit these new input files as large files, sync the
metadata to all remotes, and then copy the content of the new files to `taco`. 

NB: git annex must be installed on a remote to sync large file content. If
a remote doesn't support git-annex (e.g. GitHub), then `git annex sync` will
just sync the metadata even if you ask it to sync content, and you won't be
able to use `git annex get/copy`.

#### Special remotes

Git annex support [special
remotes](https://git-annex.branchable.com/special_remotes/), a generalised way
of storing large file content. Unlike `taco` above, these remotes aren't
typical git repositories, but are file stores that contain *only* large files'
content. One could for example use [Backblaze's B2 cloud
storage](https://www.backblaze.com/b2/cloud-storage.html) to make some or all
large files available to the wider world, without giving access to the local
HPC storage, and without paying for the exorbitant storage and bandwidth costs
of git-lfs. See the [git-annex
docs](https://git-annex.branchable.com/special_remotes/) for more info.


## Quick-start cheatsheet:


### A `curl|sh` for installing git-annex

Yes, this is a bad idea. Yes, everybody does it anyway. On modern linux systems, running

```
curl -LSs https://gist.github.com/kdm9/2c715a2e4fc1c4f93bb22c0beba6d73e/raw/get-annex.sh | bash
```

will install git-annex standalone under `~/.local/opt`, with symlinks to
`~/.local/bin`. It will also check if you have that directory on your `$PATH`,
and suggest a way to ensure you do if that's not the case.

### Common commands

```bash
# Add a file to be tracked by git. If it's a large file (see configuration in
# .gitattributes above), then add it as an immutable, locked copy.
git annex add some/directory/  ./or-files.txt

# Unlock an immutable large file, so that you can edit or overwrite it
git annex unlock ./directory/ ./or-file.txt

# Save changes to an unlocked file (same as adding it the first time)
git annex add ./blah

# Discard changes to an unlocked file, reverting it to the previous copy
# (dangerous, so it needs --force. run without force to see what it would do)
git annex lock --force ./blah

# Commit changes
git commit -m "add modelling inputs"

# Send and receive the changes to code and knowledge about the existance of any
# large files (but *not* their content)
git annex sync

# Push all changes, including the actual content of large files. You can give
# a list of remotes to push to, in this case 'github' and 'largeserver'. git
# annex will push code to all repositories, and large files to any repository
# set up to recieve large files (e.g. a server or special remote, but not
# github or similar sites).
git annex push github largeserver

# Copy large files to some remote (you can give directories or paths to files)
git annex copy --to taco notebook/data/2_modelling

# Get large files from some remote that contains them
git annex get path/to/dir ./and-files.txt
# (if need be, you can use --from with git annex get to force it to use e.g.
# a local server instead of one at some remote site)

```



[^1]: Others, like git-lfs, are designed to work with cloud-based git hosting
    platforms like GitHub. Typically, git-lfs users store the large files they
    track on e.g. Github. The costs of these services are significant: to store
    the aforementioned 12TB of raw data from our *Arabidopsis* project on
    GitHub would cost USD1200 per month!!!. Git-annex is far more flexible with
    how large datasets are stored and shared, allowing us to use our local HPC
    resources for storage.

[^2]: See the `git annex sync --commit`, `--no-commit` CLI options, and 
      `git config annex.autocommit` to alter this behaviour.

