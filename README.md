# dotfiles

Managed with [chezmoi](https://chezmoi.io). Source dir: `~/dotfiles`.

## Bootstrap a new machine

```sh
# 1. Install Homebrew (https://brew.sh) and chezmoi:
brew install chezmoi

# 2. Clone and apply:
chezmoi init --source=$HOME/dotfiles --apply git@github.com:<user>/dotfiles.git
```

`run_onchange_before_install-packages.sh.tmpl` will run `brew bundle` from the
embedded `Brewfile` and install everything before any other config is applied.

## Daily use

| What | Command |
| --- | --- |
| Edit a managed file | `chezmoi edit ~/.zshrc` |
| Apply pending changes | `chezmoi apply` |
| Diff what would change | `chezmoi diff` |
| Add a new file to chezmoi | `chezmoi add ~/.somefile` |
| Jump into the source repo | `chezmoi cd` |
| Re-snapshot installed brews | `cd ~/dotfiles && brew bundle dump --force` |

## Layout

```
~/dotfiles/
├── Brewfile                                       # source of truth for brew packages
├── dot_zshrc                                      # → ~/.zshrc
├── dot_zprofile                                   # → ~/.zprofile
└── run_onchange_before_install-packages.sh.tmpl   # runs `brew bundle` on Brewfile change
```

## Secrets

Secrets are NOT in this repo. They live in `~/.config/zsh/secrets.zsh` (mode
0600), which `~/.zshrc` sources at the end if present. To bootstrap a new
machine: re-create that file by hand and put any tokens there.
