# config

`config` is a configuration management pattern in Shell for Linux users. `base-config` is the instance of `config` that contains files that are most likely to be useful in any Linux instance in the domain(s) a user works in. This `base-config` and the `wayland-config` are customized to myself&mdash;modify to your needs.

## TODO

### config
- Refactor to Nix

### `wayland-config`
- GTK/QT universal theme

## Install a config

Assign `config_repo` to something like `base-config` or `wayland-config` then do

```shell
cd
git clone https://github.com/dl0461/"$config_repo".git
```

Read `"$config_repo/.config/shell/$config_repo/dependencies"`.

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

#### `init-more`

See `.config/shell/base-config/init-more` for an example `init-more`. This kind of script does tasks that should be done before a `zprofile` is sourced.

#### `cmdpatterns`

This file contains newline delimited Python `Lib/re` compatible regular expressions that denote what shell commands should be kept in `$XDG_STATE_HOME/zsh/histfile`.

#### `zprofile.sh`

This file can have `ssh-add` statements in it.

If `cmdpatterns` is used, this file should have this block in it:

```shell
if [ -f "$CFG/shell/$config_repo/cmdpatterns" ]; then
    shellhistoryfilter_hook() {
        shellhistoryfilter.py "$CFG/shell/$config_repo/cmdpatterns"\
            &> "$XDG_STATE_HOME/zsh-shellhistoryfilter-hook-log"
    }
    add-zsh-hook zshexit shellhistoryfilter_hook
fi
```

#### `zshrc.sh`

This file contains any tasks that should be executed in `.zshrc`.

### Unique Scripts and Binaries

```shell
mkdir ~/.local/bin/"$config_repo"
```

This directory contains any scripts specfic to `$config_repo`. It will have at least one script:

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
init-any-config "$config_repo"
```
