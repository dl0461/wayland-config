# Config

This is a pattern of configuration management in Shell for Linux users. `base-config` is the instance of this pattern that contains domain unspecific files. [`sgy4q4ygs/base-config`](https://github.com/sgy4q4ygs/base-config) and [`sgy4q4ygs/wayland-config`](https://github.com/sgy4q4ygs/wayland-config) are unique to one&mdash;modify to your needs. Note that there are nicer ways to do personal configuration management i.e. this is an experiment.

## Install a config

Assign `config_repo` to something like `base-config` or `wayland-config` then do

```shell
cd
git clone https://github.com/sgy4q4ygs/"$config_repo".git
```

If `config_repo` is not `base-config`

```shell
init-config "$config_repo"
```

else

```shell
. "$config_repo/.local/bin/$config_repo/init-config" "$config_repo"
```

now logout.

## Use a config

Replace `git [commands]` with `git-"$config_repo" [commands]` to use a config repo.

## Create a config

The name of a config is of the form `*-config`.

### Shell Configuration

```shell
mkdir ~/.config/shell/"$config_repo"
```

This directory can have these files:

#### `dependencies`

This script defines, downloads, and installs dependencies of a config. It is executed by `init`.

#### `init`

This kind of script does tasks that should be done before a `zprofile` is sourced. It can be setup to execute `dependencies` only once or every time `init` is executed.

#### `cmdpatterns`

This file contains newline delimited Python `Lib/re` compatible regular expressions that denote what shell commands should be kept in `$XDG_STATE_HOME/zsh/histfile`.

#### `zprofile.sh`

This file can have `ssh-add` statements in it.

#### `zshrc.sh`

This file contains any tasks that should be executed in `.zshrc`.

### Special Programs

```shell
mkdir ~/.local/bin/"$config_repo"
```

This directory contains any programs specfic to `$config_repo`. It will have at least one script:

```shell
cat << EOL > ~/.local/bin/"$config_repo/git-$config_repo"
#!/usr/bin/env sh
git --git-dir="$HOME/$config_repo" "$@"
EOL
```

### File Exclusion

Create an exclude template:

```shell
cat << EOL > ~/.config/git/exclude-"$config_repo"
/*

!/.config
/.config/*

!/.config/git
/.config/git/*
!/.config/git/config
!/.config/git/hooks
!/.config/git/exclude-"$config_repo"

!/.config/shell
/.config/shell/*
!/.config/shell/"$config_repo"

!/.local
/.local/*

!/.local/bin
/.local/bin/*
!/.local/bin/"$config_repo"

!/README.md
EOL
```

After an `exclude-"$config_repo"` is edited,

```shell
init-config "$config_repo"
```
