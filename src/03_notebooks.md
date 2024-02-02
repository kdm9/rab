# Literate Analysis

Literate Programming is a set of practices introduced by Donald Knuth (of `C`, and `.tex` fame), in which code isn't something simply dictated to a computer. Rather, code forms a larger document, which interleaves instructions to the computer (code), as well as one's thoughts (prose), and the results of whatever we asked the computer to do (outputs).

I hope it's immediately clear to you that this paradigm is highly applicable to us as computational scientists: while we care about the specifics instructions we give to a computer, we care equally about the outputs, and about the deeper scientific meaning of an analysis.

Computational notebooks allow us to perform our analyses in a literate fashion, combining code, outputs, and prose. Below, I outline a few systems for literate analysis, before showing you how to work with my preferred system, Jupyter.

## Systems for literate analyses

You've probably heard of a few different ways one can interleave code and outputs. In R, [Rmarkdown](https://rmarkdown.rstudio.com/) is the canonical way of doing this. In python, most people use [IPython/Jupyter](https://jupyter-notebook.readthedocs.io/en/stable/notebook.html). In Julia, one could use [`Pluto.jl`](https://plutojl.org/). There are also many others, including [Quarto](https://quarto.org/), [Jupytext](https://github.com/mwouts/jupytext), [Sweave](), and [Observable](https://observablehq.com/).

After using nearly all of these, I've settled on the Jupyter ecosystem. For me at least, it has the following benefits:

- **Server-based, but accessible over the web.** This means one can do your computation on some big beefy server, but you're still typing and viewing things on your laptop, in your web browser.
- **Multilingual, with a consistent interface.** In bioinformatics, the best solution for any given problem will probably be used via one of R, python, Julia, or shell. Therefore, we want a consistent way to use all of these languages. So, as much as I love `Rmarkdown` and Pluto, as language-specific tools they don't cut it for me.
- **Good tooling.** This is less of a differentiator, because many competing tools also have a great ecosystem of tools, but somebody has written a tool to do nearly anything you'd want with a Jupyter notebook.


## Getting started with Jupyterlab

As discussed above, the Jupyter ecosystem is my preferred way of doing literate computational analyses. Specifically, I use the JupyterLab server, with Jupyter notebooks for `python`, `IRkernel` for R notebooks, and `bash_kernel` for shell. There are of course many more possible "kernels", or language adaptors/addons, including for [Julia](https://github.com/JuliaLang/IJulia.jl) and nearly any other language you can think of (a full list [is here](https://github.com/jupyter/jupyter/wiki/Jupyter-kernels)). If you are completely new to Jupyter, and would like a brief introduction to the notebook interface, I suggest [this tutorial](https://jupyter-notebook.readthedocs.io/en/stable/ui_components.html). 

Below, I'll guide you through setting up JuptyerLab, with Python, R, and Bash kernels configured. You can run these commands on a nice beefy server, or on your local machine. I prefer to use nice big servers, where data lives, not a laptop with limited compute & storage (unless your data needs are small), although I still use this same setup on my laptop for when I'm doing something locally.

First, we need to install the Jupyter
```bash
python3 -m pip install -U pip wheel
python3 -m pip install -U jupyterlab nbconvert jupytext bash_kernel papermill
python3 -m bash_kernel.install
Rscript -e 'install.packages("IRkernel"); IRkernel::installspec()'
```

Next, we must set up and launch our server. First, we have to set a password, so that we can more simply log in. Then, we start the JupyterLab server in a new Tmux session (see the next session). You should pick a unique port number (between 1025 and 65000), which you will need to remember (or bookmark). Replace `YOUR_PORT` with your actual port number in the commands below.

```bash
jupyter server password # enter a NEW password here
tmux new-session jupyter lab --port YOUR_PORT --no-browser
```

Then you should be all set up. Go to your server's address, e.g. `http://server.address.blah:YOUR_PORT` (or `http://localhost:YOUR_PORT` if running on your laptop) in a modern browser (Firefox/Chromium/Safari). It will ask for your password, which will be the one you set with `jupyter server password`, *NOT* your normal password for that server or laptop.


For remote servers, one should use SSH forwarding to access your server. Specifically, use `ssh -N -L YOUR_PORT:localhost:YOUR_PORT username@server.address.blah` (obviously replace that with your username, server, and port), and access JupyterLab via `http://localhost:YOUR_PORT`, rather than the server URL above. [Autossh](https://linux.die.net/man/1/autossh) would be useful here if you are on Linux or Mac -- it will monitor your ssh connection, and restart it if the connection drops, e.g due to moving between wifi & ethernet.
JupyterLab and commands run from it will still run on the remote server, but you connection will now be encrypted and folks on the same network as the compute server won't be able to access the JupyterLab instance directly. This is very important if you run on an untrusted network or if running JupyterLab e.g. in the cloud.

We run JupyterLab within tmux so that it will persist past the current ssh connection and we still can easily see its output. If you don't know or want tmux, you could use `screen` or even `nohup` to get JupyterLab to persist, or read the next chapter where I cover tmux :).


## Advanced Jupyter

One real power of Jupyter is the ecosystem of extensions and tools that supercharge the core notebook concept. Here, I briefly introduce some of those that I use.

### `juptyerlab-vim`: vi-like keybindings for JupyterLab

As someone who is welded to the terminal, I'm lost without Vim, and so whenever I use some other software I immediately install some vim keybindings. If you don't know what this means, ignore this point, however I strongly recommend you check out vim, perhaps with the command `vimtutor`. 

### `nbconvert`: notebook format conversion

[`jupyter nbconvert`](https://nbconvert.readthedocs.io/en/latest/index.html) is the swiss-army-knife of format conversion to/from jupyter notebooks. I typically use it to generate static HTML files from a notebook, that I can then share with a collaborator over the web. It can also convert to pdfs, or be used to generate markdown documents or other source documents for a blogging or writing system. There are also plugins to any common documentation or blog static site generator that use nbconvert to support pages/posts written as a Jupyter notebook, e.g. for [hugo](https://knowsuchagency.github.io/hugo_jupyter/) or [mkdocs](https://pypi.org/project/mkdocs-jupyter/). 


### `papermill`: parameterisation of notebooks

If you ever feel the need to run a notebook multiple times with different parameters, or input files, then take a look at [`papermill`](https://papermill.readthedocs.io/en/latest/).

### `jupytext`: script <-> notebook interoperation

One downside of Jupyter notebooks is that they are somewhat opaque objects, consisting of a large JSON document of inputs, outputs, and various other metadata. Sometimes, we need to work with scripts. Jupytext is a tool that transparently converts between scripts and notebooks. You can run a script as though it was a jupyter notebook, or write a script-ified version of your notebook whenever you save the real notebook.

It plays especially nicely with papermill, allowing one to write a script as per normal, and then parameterise it and run it as a Jupyter notebook, capturing all outputs.

### `nbdime`: Nice diffs & merging of jupyter notebooks

As mentioned before, Jupyter notebooks are just massive JSON documents. This makes diffing, merging, and tracking changes to notebooks more complex than would be ideal. Tools like [`nbdime`](https://github.com/jupyter/nbdime) allow one (or git) to more easily see difference between multiple notebooks, or between versions of one notebook.

