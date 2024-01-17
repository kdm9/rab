# Workspaces and Data Hygene

When I say "workspace", I speak of a directory within which some computational analyses happen. If you're used to working in R, an Rstudio project is a great example of a workspace, and Rstudio provides a set of tools and conventions that help us keep track of our code, data, metadata, and results.

Let's discuss key features of a well-organised workspace. Ideally, our workspaces should be:

- **Self-contained:** one should keep data, metadata, code, and outputs all under one project-specific directory. This also helps keep our workspaces **Sharable**.
- **Sharable:** one should be able to send a workspace to a supervisor/collaborator/colleague with minimal fuss (data volume notwithstanding), and for them to be able to run this workflow and generate the same results. Shareability is a requirement for **Reproducibility**.
- **Reproducible:** Someone else (a reviewer, for example) should be able to convince themselves of your findings by running your code themselves, possibly with modifications. Byte-for-byte reproducibility is less important than being able to quickly install all software, download all data, and then run the exact analysis steps that you ran.

As discussed in the introduction, bear in mind that these goals primarily benefit ourselves: Having sharable, self-contained workspaces mean that we know where all data and code for a given project is, and can move the analysis between computer systems as needed. Ensuring a workspace is reproducible helps us resurrect it after some time, either to recompute an existing finding, or to reuse the analysis or dataset for a new project.

## Data Hygene

Data hygene refers to a set of good practices that help keep our workspace self-contained, sharable and reproducible.

`tl;dr:`

- Step 0 of all analyses is to BACK UP RAW DATA NOW!!! Be a data squirrel.
- Keep raw data and output separate, ideally with raw data and code on a backed-up redundant file system, and intermediate and output files on fast temporary storage.
- Record every step of your analyses, and write documentation of what each major output means *to you*.
- Use version control to manage each workspace, recording all versions of an analysis and making sharing a workspace trivial.


### Backups

Step 0 of all analyses is to BACK UP RAW DATA NOW!!! Be a data squirrel. There is a lot of theory around backups, but some tips: don't trust your data solely to institutional IT systems! They have a lot going on, are often understaffed, and are often more experienced with running a fleet of windows laptops than dealing with HPC and data analysis. At very least, keep your raw data on a hard drive along with some checksums to verify its integrity. A 20TB external hdd costs about 1% of the money you'd pay in sequencing to fill it -- a 1% data safety tax should be doable on nearly any budget.


### Separate raw and recreatable data

A workspace should keep recreateable data separate from raw data. Recreatable data is anything your code produces. In an ideal world, you should be able to `rm -rf` your output directory, and run only one or a few commands and recreate the same result (using either scripts, notebooks, or a workflow manager; we'll cover this in later sections). I suggest keeping raw and recreatable data in separate directories, e.g. `./rawdata` and `./outputs`. Typically, we want to keep recreateable data on some temporary SSD storage that is not backed up, while code and raw data is stored on a backed-up and redundant filesystem, and you can achieve this for example by symlinking a directory on temporary storage to `./output`, so all output are placed on some temporary file system.

The recreatable data directory (e.g. `./outputs`) should ONLY contain recreateable data, such that you can do `rm -rf ./outputs` with impunity. Never get tempted to mix code or other ad-hoc but important things in the output directory, especially if it is on temporary storage.


### Record your work

When we first learn to use the command line (or R, or even Python), we typically work in an ephemeral console or [REPL](https://en.wikipedia.org/wiki/Read%E2%80%93eval%E2%80%93print_loop). The comands run and any output they create is lost as soon as you close the window or leave the session. This is rather bad, because I guarantee that at some point, you'll need to either remember that command or its output. Therefore, one should always record what was done.

Ideally, this is done automatically by using one of several methods of running code that inherently record what was done. One could use **scripts**, in which one specified exactly what to run ahead of time. Better yet, methods like [RMarkdown](https://rmarkdown.rstudio.com/), [Quarto](https://quarto.org/), or [Jupyter](https://jupyter.org/) allow one to interactively perform analyses, but record both the code that was ran, and any output. Lastly, for batch analysis of huge data, workflow management engines like [Snakemake](https://snakemake.readthedocs.io/), [CWL](https://www.commonwl.org/), or [Nextflow](https://www.commonwl.org/) enable efficient and reproducible analyses at very large scale.


### Documentation and journaling

Above, we 

- Document why things are being done the way they are with comments in scripts/Snakefiles
- Use jupyter notebooks, markdown files, or labarchives as a lab notebook. Notebooks and scripts have different purposes -- notebooks are for you, scripts are for the computer.


- Use tmux to maintain persistent sessions on a remote server, one per project

### Version control
- Use git to track code, metadata, and documentation, but not log files or data. We'll cover this later

