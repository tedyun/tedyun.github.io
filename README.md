# tyun.io

## Setting up local environment

1. Install `rbenv`.
  * Use [rbenv-installer](https://github.com/rbenv/rbenv-installer#rbenv-installer) (`curl -fsSL https://github.com/rbenv/rbenv-installer/raw/master/bin/rbenv-installer | bash`).
  * Add the following lines in `~/.bashrc` (Linux) or `~/.bash_profile` (Mac OS):
```
# Ruby and rbenv (tedyun)
export PATH="$HOME/.rbenv/bin:$PATH"
eval "$(rbenv init -)"
```
2. Check the preferred Ruby version in [this page](https://pages.github.com/versions/), install & activate that version of Ruby using `rbenv`.
3. Run `setup.sh`.

## Usage

* Use `math: true` in posts to enable mathjax.
* Use `vis: true` in posts to load visualization libraries in `_includes/vis_lib.html` (jQuery, d3, etc.)
