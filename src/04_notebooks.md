# Notebooks

# Jupyterlab
A quick-start list of commands for a fully-configured Jupyterlab user install that supports bash, R, and python.

<!--more-->

You can run these commands on a nice beefy server, or on your local machine. I prefer to use nice big servers, where data lives, not a laptop with limited compute & storage (unless your data needs are small). For WeigelWorld folks: that would be `taco` or `chimi`.

```bash
/usr/bin/python3 -m pip install -U pip
/usr/bin/python3 -m pip install -U jupyterlab nbconvert jupytext bash_kernel papermill
/usr/bin/python3 -m bash_kernel.install
Rscript -e 'install.packages("IRkernel"); IRkernel::installspec()'
jupyter server password # enter a NEW password here
tmux new-session jupyter lab --ip 0.0.0.0 --port YOUR_PORT --no-browser
```

Then you should be all set up. Go to your server's address (like `http://chimi.eb.local:YOUR_PORT` or `http://localhost:YOUR_PORT` if running on your laptop) in a modern browser (Firefox/Chromium/Safari). It will probably ask for your password, which should be the one you set with `jupyter server password`, *NOT* your normal password for that server or laptop.

Some notes:

- You should pick a unique port number (between 1025 and 65000), which you will need to remember (or bookmark). Replace `YOUR_PORT` with your actual port number in the commands above and below.
- We run JupyterLab within tmux so that it will persist past the current ssh connection and we still can easily see its output. If you don't know or want tmux, you could use `screen` or even `nohup` to get Jupyterlab to persist.
- For remote servers, a more secure alternative is to omit `--ip 0.0.0.0` in the `jupyter lab` command above, and always use SSH forwarding. One would then use `ssh -N -L YOUR_PORT:localhost:YOUR_PORT ldap_id@chimi.eb.local`[^1] (obviously replace that with your username and server), and access JupyterLab via `http://localhost:YOUR_PORT`, rather than the server URL above. JupyterLab and commands run from it will still run on the remote server, but you connection will now be encrypted and folks on the same network as the compute server won't be able to access the JuptyerLab instance directly. This is very important if you run on an untrusted network or if running Juptyerlab e.g. in the cloud.


[^1]: [autossh](https://linux.die.net/man/1/autossh) would be useful here if you are on Linux or Mac.
