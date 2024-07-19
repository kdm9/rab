# Literate Analyses with Computational Notebooks

Literate Programming is a set of practices introduced by Donald Knuth (of `C`, and `.tex` fame), in which code isn't something simply dictated to a computer. Rather, code forms a larger document, which interleaves instructions to the computer (code), as well as one's thoughts (prose), and the results of whatever we asked the computer to do (outputs).

I hope it's immediately clear to you that this paradigm is highly applicable to us as computational scientists: while we care about the specifics instructions we give to a computer, we care equally about the outputs, and about the deeper scientific meaning of an analysis.

Computational notebooks allow us to perform our analyses in a literate fashion, combining code, outputs, and prose. Below, I outline a few systems for literate analysis, before showing you how to work with my preferred system, Jupyter.

## Systems for literate analyses

You've probably heard of a few different ways one can interleave code and outputs. In R, [Rmarkdown](https://rmarkdown.rstudio.com/) is the canonical way of doing this. In python, most people use [IPython/Jupyter](https://jupyter-notebook.readthedocs.io/en/stable/notebook.html). In Julia, one could use [`Pluto.jl`](https://plutojl.org/). There are also many others, including [Quarto](https://quarto.org/), [Jupytext](https://github.com/mwouts/jupytext), [Sweave](https://en.wikipedia.org/wiki/Sweave), and [Observable](https://observablehq.com/).

After using nearly all of these, I've settled on the Jupyter ecosystem. For me at least, it has the following benefits:

- **Server-based, but accessible over the web.** This means one can do your computation on some big beefy server, but you're still typing and viewing things on your laptop, in your web browser.
- **Multilingual, with a consistent interface.** In bioinformatics, the best solution for any given problem will be used via one of R, python, Julia, or shell. Therefore, we want a consistent way to use all of these languages. So, as much as I love `Rmarkdown` and `Pluto`, as language-specific tools they don't cut it for me, as I would need to have N setups for N languages.
- **Good tooling.** This is less of a differentiator, because many competing tools also have a great ecosystem of tools, but somebody has written a tool to do nearly anything you'd want with a Jupyter notebook.


## Getting started with Jupyterlab

As discussed above, the Jupyter ecosystem is my preferred way of doing literate computational analyses. Specifically, I use the JupyterLab server, with Jupyter notebooks for `python`, `IRkernel` for R notebooks, and `bash_kernel` for shell. There are of course many more possible "kernels", or language adaptors/add-ons, including for [Julia](https://github.com/JuliaLang/IJulia.jl) and nearly any other language you can think of (a full list [is here](https://github.com/jupyter/jupyter/wiki/Jupyter-kernels)). If you are completely new to Jupyter, and would like a brief introduction to the notebook interface, I suggest [this tutorial](https://jupyter-notebook.readthedocs.io/en/stable/ui_components.html). 

Below, I'll guide you through setting up JuptyerLab, with Python, R, and Bash kernels configured. You can run these commands on a nice beefy server, or on your local machine. I prefer to use nice big servers, where data lives, not a laptop with limited compute & storage (unless your data needs are small), although I still use this same setup on my laptop for when I'm doing something locally.

### Apptainer

If your server/cluster/laptop supports it, I recommend using [apptainer](https://apptainer.org/) to run a notebook container I have prepared. This ensures you have a recent version of both Python3 and R, even on older systems. With Apptainer, there is no installation step: Apptainer will automatically pull the image when you go to run it. Therefore, one can simply run the following within a [tmux or screen](./terminal.md#tmux) session:

```bash
apptainer exec \
	-B /tmp/global2 -B /ebio \
	docker://ghcr.io/kdm9/notebooks:latest \
	jupyter lab --no-browser --port $YOUR_PORT --ip 0.0.0.0

```

Please set `--ip` and `--port` to your situation. An IP of 0.0.0.0 is equivalent to a wildcard, i.e. run on both the local and externally-accessible server adresses. Your port should be any number between 1025 and 65000 that you can remember (and isn't already taken by someone else on that server). The `-B` arguments map paths between the host and the container running Jupyter -- please adjust these to include any directory you'll need to access, including host `/tmp` filesystems, NFS mounts, and your home dir. Additionally, conda is installed to `/opt/conda`, so add the following to your `~/.bashrc` or `~/.bash_env` to enable the use of conda within Jupyter notebooks.

```
if [ -f "/opt/conda/etc/profile.d/conda.sh" ]
then
    . "/opt/conda/etc/profile.d/conda.sh"
fi

if [ -f "/opt/conda/etc/profile.d/mamba.sh" ]
then
    . "/opt/conda/etc/profile.d/mamba.sh"
fi
```

You may notice that there is strange prompt in place of your normal bash prompt. If this bothers you, add `export SINGULARITYENV_PS1="[singularity] $PS1"` to your `~/.bashrc`, and re-launch Jupyter as above (you can always just hit control-c to cancel it). Another note: I have `jupyterlab-vim` installed in this image, providing vim-like keybindings. If this is not to your taste, you can disable (but not uninstall) it according to [these instructions](https://github.com/jupyterlab-contrib/jupyterlab-vim/issues/142#issuecomment-2211175299) (`Settings` menu → `Settings Editor` → `Notebook Vim` → Uncheck both `Enabled` and `Enabled in Text Editor`).

### Pip-based install

First, we need to install the relevant bits of the Jupyter ecosystem, including the Jupyter core, JupyterLab, and kernels for R, Bash, and Python (add any other languages you want supported here, see discussion above/[this list](https://github.com/jupyter/jupyter/wiki/Jupyter-kernels)).

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


## Using Jupyter

Then you should be all set up. Go to your server's address, e.g. `http://server.address.blah:YOUR_PORT` (or `http://localhost:YOUR_PORT` if running on your laptop) in a modern browser (Firefox/Chromium/Safari). It will ask for your password, which will be the one you set with `jupyter server password`, *NOT* your normal password for that server or laptop.


For better security with remote servers, especially internet-accessible ones, one should use SSH forwarding to access your server. Specifically, give `--ip 127.0.0.1` above, and use `ssh -N -L YOUR_PORT:localhost:YOUR_PORT username@server.address.blah` (obviously replace that with your username, server, and port). You can then access JupyterLab via `http://localhost:YOUR_PORT`, rather than the server URL above. [Autossh](https://linux.die.net/man/1/autossh) would be useful here if you are on Linux or Mac -- it will monitor your ssh connection, and restart it if the connection drops, e.g due to moving between wifi & ethernet. JupyterLab and commands run from it will still run on the remote server, but you connection will now be encrypted and folks on the same network as the compute server won't be able to access the JupyterLab instance directly. This is very important if you run on an untrusted network or if running JupyterLab e.g. in the cloud.

We run JupyterLab within tmux so that it will persist past the current ssh connection and we still can easily see its output. If you don't know or want tmux, you could use `screen`, [`supervisord`](https://supervisord.org/), or even `nohup` to get JupyterLab to persist.


## Advanced Jupyter

One real power of Jupyter is the ecosystem of extensions and tools that supercharge the core notebook concept. Here, I briefly introduce some of those that I use, and come pre-installed in the notebook docker/apptainer image I provide above.

### `juptyerlab-vim`: vi-like keybindings for JupyterLab

As someone who is welded to the terminal, I'm lost without Vim, and so whenever I use some other software I immediately install some vim keybindings. If you don't know what this means, ignore this point, however I strongly recommend you check out vim, perhaps with the command `vimtutor`. As mentioned above, to disable vim keybindings: `Settings` menu → `Settings Editor` → `Notebook Vim` → Uncheck both `Enabled` and `Enabled in Text Editor`.

### `nbconvert`: notebook format conversion

[`jupyter nbconvert`](https://nbconvert.readthedocs.io/en/latest/index.html) is the swiss-army-knife of format conversion to/from Jupyter notebooks. I typically use it to generate static HTML files from a notebook, that I can then share with a collaborator over the web. It can also convert to PDFs, or be used to generate markdown documents or other source documents for a blogging or writing system. There are also plugins to any common documentation or blog static site generator that use `nbconvert` to support pages/posts written as a Jupyter notebook, e.g. for [hugo](https://knowsuchagency.github.io/hugo_jupyter/) or [mkdocs](https://pypi.org/project/mkdocs-jupyter/). 


### `papermill`: parameterisation of notebooks

If you ever feel the need to run a notebook multiple times with different parameters, or input files, then take a look at [`papermill`](https://papermill.readthedocs.io/en/latest/). In summary, you set variables in a cell marked with a special notation, and then this cell will be updated based on the values given as command line arguments. This works with (at least) bash, R, python, and Julia notebooks.

### `jupytext`: script <-> notebook interoperation

One downside of Jupyter notebooks is that they are somewhat opaque objects, consisting of a large JSON document of inputs, outputs, and various other metadata. Sometimes, we need to work with scripts, for example in submitting to a cluster (though see `papermill` above, which works well for cluster jobs). Jupytext is a tool that transparently and bidirectionally converts between scripts and notebooks. You can run a script as though it was a Jupyter notebook, or write a script-ified version of your notebook whenever you save the real notebook.

It plays especially nicely with papermill, allowing one to write a script as per normal, and then parameterise it and run it as a Jupyter notebook, capturing all outputs.

### `nbdime`: Nice diffs & merging of jupyter notebooks

As mentioned before, Jupyter notebooks are just massive JSON documents. This makes diffing, merging, and tracking changes to notebooks more complex than would be ideal. Tools like [`nbdime`](https://github.com/jupyter/nbdime) allow one (or git) to more easily see difference between multiple notebooks, or between versions of one notebook.

