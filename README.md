# tyun.io

## Setting up local environment

1. Install `rbenv`.
  * Use [rbenv-installer](https://github.com/rbenv/rbenv-installer#rbenv-installer) (`curl -fsSL https://github.com/rbenv/rbenv-installer/raw/HEAD/bin/rbenv-installer | bash`).
  * (No longer required - the installer handles this part.) Add the following lines in `~/.bashrc` (Linux) or `~/.bash_profile` (Mac OS):
```
# Ruby and rbenv (tedyun)
export PATH="$HOME/.rbenv/bin:$PATH"
eval "$(rbenv init -)"
```
2. Check the preferred Ruby version in [this page](https://pages.github.com/versions/), install & activate that version of Ruby using `rbenv`. Note this may require some C compilers and libraries (`sudo apt install build-essential libz-dev`). For example:
```
$ rbenv install 3.3.4
$ rbenv global 3.3.4
```
3. Run `setup.sh`.

## Starting a local server

After setting up local environment as above, run `run_server.sh`.

## Usage

* Use `math: true` in posts to enable mathjax.
* Use `vis: true` in posts to load visualization libraries in `_includes/vis_lib.html` (jQuery, d3, etc.)
