# Modern Terminal Tools


While a lot of our work is done in computational notebooks, we still do need to use the terminal for quite a lot of things. In this chapter I'll give a quick overview of some tools I use to on the command line, that make life as a bioinformatician much easier.

IN PROGRESS!!! :)

## Conda

(probably going to be its own section)

A ecosystem for installing pre-compiled packages, and managing virtual environments. Conda works across programming languages, and has bioinformatics-specific teams/projects.

For biologists: follow the install instructions for [bioconda](https://bioconda.github.io/), installing the [miniforge](https://github.com/conda-forge/miniforge) conda distribution and enabling the `bioconda` and `conda-forge` channels. For general compsci/data science folks, you're probably OK with just conda-forge.

## rsync

[Rsync](https://wiki.archlinux.org/title/rsync) is **the** way to copy files on the command line. It has a lot of features, and is generally safer and easier to use than e.g.`cp`, particularly when using the recommended args. For synchronising two directories, I use `rsync -rhivaP SOURCE_DIR/ DEST_DIR/` (note the [trailing slash on the source](https://wiki.archlinux.org/title/rsync#Trailing_slash_caveat)). Adding `--del` to the args will cause files to be deleted from `DEST_DIR/` if they aren't in `SOURCE_DIR/`. Adding `--remove-source-files` will remove all files from `SOURCE_DIR/` once they have been copied to `DEST_DIR/`.

If you're working with any kind of cloud storage, [rclone](https://rclone.org/) is a phenomenal tool that interoperates with nearly any cloud storage provider, and allows copying, syncing, and moving files around.


## Vim

Vim is a terminal mode editor and development environment.  Start by running `vimtutor` on pretty much any modern Unix (because vim is nearly everywhere!). Yes, it will be incredibly frustrating for the first about 20 minutes, that is normal. But trust me, once you get your head around it, you'll wonder how you ever did without it.

## Tmux

Tmux gives you tabs and sessions that persist across logins to a server. For those old enough to remember a time before tabs in web browsers, you'll know the dramatic improvements to productivity they bought. Tmux brings those benefits to the command line. Other tools like screen also do this, but I highly recommend learning tmux as it has more features that you can grow into, including split panes, easier session management, and better customisation. I recommend printing a [tmux cheat-sheet](https://opensource.com/downloads/tmux-cheat-sheet) while learning, Tmate is a tmux extension that allows direct collaboration and remote debugging in a team.

## NCDU

[NCDU](https://dev.yorhel.nl/ncdu) is a tremendously useful tool that allows semi-graphical exploration of a folder and all contents. It's super useful while cleaning up workspaces, or exploring some dataset.


## rmlint

[Rmlint](https://github.com/sahib/rmlint) is a tool to de-duplicate files, and clean up any weird 'lint' from the file system. Use with caution, but it's one of the safest tools for what is a surprisingly error-prone task.

## Zsh

An alternative shell that improves productivity. Zsh comes with a lot of time-saving shortcuts that Bash lacks. Unfortunately, zsh typically requires an admin to install on a cluster or server, so isn't as prevalent as would be ideal. That said, on a local machine or well-administered cluster, you should be able to use zsh as your shell. Some advantages:

- Better alias and tab completion support
- fuzzy matching during tab completion
- time-saving niceties like `...` as a shortcut for `../..`, and automatically cd-ing (running `dir/name`  is equivalent to `cd dir/name`).

So, switching gets you a bunch of minor conveniences for free. That said, there are ways of configuring bash to have some of these features.
