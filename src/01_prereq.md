# Prerequisite knowledge

This is a book by a biologist, and is written primariyly for biologists. While this book is important for all levels of computational experience, to keep this book focused I assume a comfortable level of familiarity with the following comptuational topics, few of which a biologist's training typically covers. If this is your first real foray into heavy computational work, I highly recommend taking the time now to learn or revise the following topics. The remainder of this book will make a lot more sense with this knowledge under your belt, and you'll be a better and more producitve computational biologist for it.

Overall, I *highly* recommend you do at least one of the following complete courses:

- The [Software Carpentry "quartet" of courses on shell, git, python, and R](https://software-carpentry.org/lessons/)
- [Learn X in Y minutes](https://learnxinyminutes.com/) are fantastic but terse cheat sheets or refresher courses for many programming languages and tools like git. These are ideal for those who once learned a topic that has since fallen from their brain, more so that those learning from scratch.
- [MIT CSAIL's "Missing Semester"](https://missing.csail.mit.edu/) course on "the stuff they don't teach you": shell, git, vim, debugging. More for comp sci students, but all very useful stuff.


## Bash/Shell

If you can't confidently answer the following questions, brush up on your shell knowledge with the resources below.

- What do `cd`, `ls`, and `mkdir` do?
- If I am in the directory `/home/kevin/work/test`, name two ways to change directory to `/home`
- How do I set the variable `frog` to the string value `Litoria`?
- How do I print the value of the variable `frog`?
- How do I loop through the numbers 1 to 10? And how about the looping through the files in the current directory?

If there were bits of the above you weren't confident about, consider the following:

- The [software carpentry shell-novice](https://swcarpentry.github.io/shell-novice/) course takes you from zero knowledge to the basics in a few hours.
- Learn Bash in X minutes is a very condensed refresher course for those who once knew the above, or who know all the principles involved.

## SSH, and remote servers

While not strictly needed, in pratice most of you will want to apply the lessons you learn in this book on some remote server rather than your laptop. SSH is the primary way you'll do this, so please know:

- What in general is the point of using a remote server?
- What does SSH achieve?
- Some extremely basic networking terms, like domain name, user name, and port
- How would I SSH into the server `headnode.cluster.com` as user `kevin`?
- When you SSH into a server and type some command, where does that command run?
- What is the differnece between a compute server and a cluster?

- [The Linux Handbook](https://linuxhandbook.com/ssh-basics/) course is a reasonably concise but complete introduction to SSH.
- [This SSH course](https://www.hostinger.com/tutorials/ssh-tutorial-how-does-ssh-work) isn't quite as nice but covers more details on the networking and cryptography underlying ssh.


## Basic git

If you can't answer the following, then avail yourself of the resources below:

- Approximately how does git work?
- What is a commit, and how does it differ from a branch?
- What is a staged file, and how does one stage it?
- How to I get git to send and receive changes from a remote repository?
- What's the typical cycle of git commands needed to share a change with a remote repository?

- For complete beginners, the [software carpentry git course](https://swcarpentry.github.io/git-novice/) is great
- For more advanced topics, the [git tutorial](https://git-scm.com/docs/gittutorial) itself is even better
- For a quick refresher, [Learn git in X minutes](https://learnxinyminutes.com/docs/git/) is concise yet reasonably complete


## Very basic python

At various points we'll mention certain terms, and even use small pieces of python. Being fully comfortable with the entire python language won't be needed, but ideally you'd be able to answer the following questions:

- How are variables defined, and what do the basic types `int`, `dict`, `list`, `set`, and `str` do?
- How does one output something to the terminal?
- What is a package, and how does one install a package from PyPI?

You won't need anything like the full content of the following courses to follow this book, but I highly recommend you learn at least some basics from these resources, as they'll be useful to your work as a bioinformatician.

- [Software Carpentry's Python course](https://swcarpentry.github.io/python-novice-inflammation/)
- [Dive into Python](https://diveintopython3.problemsolving.io/)
- [Learn Python in X mintues](https://learnxinyminutes.com/docs/python/)

## Some basic common bioinformatics knowledge

This is less critical, especially if this isn't your field, but I mention the following terms throuout the book. If you're unfamiliar, most of this book cites external tutorials that explain these workflows/pipelines in detail.

For example, ideally you'd be familiar with the following terms:

- Illumina/short read sequencing
- Alignment to a reference genome, and BAM files
- Variant calls, and the broader concept of population genetic variation.

As I say, these are mainly motivating examples, so if these have no relevance to your work then feel free to remain in blissful ignorance of this cursed field. But, if you're starting out as a bioinformatics student, take the time to learn these concepts as you'll need them at some point.
