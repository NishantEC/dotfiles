# dotfiles

My personal macOS shell setup, managed with [chezmoi](https://chezmoi.io).

What's in here:
- A cleaned, deduped **`.zshrc`** with [Powerlevel10k](https://github.com/romkatv/powerlevel10k), [zsh-autosuggestions](https://github.com/zsh-users/zsh-autosuggestions), and [zsh-syntax-highlighting](https://github.com/zsh-users/zsh-syntax-highlighting).
- A **`Brewfile`** as the single source of truth for installed packages.
- A **bootstrap script** that runs `brew bundle` automatically whenever the Brewfile changes.

Work-specific aliases and secrets live **outside** this repo (see [Local-only files](#local-only-files) below).

## Bootstrap a new machine

```sh
# 1. Install Homebrew: https://brew.sh
# 2. Install chezmoi:
brew install chezmoi

# 3. Clone, apply, and let chezmoi run brew bundle for you:
chezmoi init --source=$HOME/dotfiles --apply git@github.com:NishantEC/dotfiles.git

# 4. Recreate the local-only files (see section below):
mkdir -p ~/.config/zsh
cp ~/dotfiles/examples/work.zsh.example   ~/.config/zsh/work.zsh
cp ~/dotfiles/examples/secrets.zsh.example ~/.config/zsh/secrets.zsh
chmod 600 ~/.config/zsh/{work,secrets}.zsh
# then edit each to add your real aliases / tokens

# 5. Configure Powerlevel10k (one-time interactive wizard):
p10k configure
chezmoi add ~/.p10k.zsh   # bring the prompt config into the repo
```

## Daily use

| What | Command |
| --- | --- |
| Edit a managed file | `chezmoi edit ~/.zshrc` |
| Preview pending changes | `chezmoi diff` |
| Apply pending changes | `chezmoi apply` |
| Add a new file to chezmoi | `chezmoi add ~/.somefile` |
| Jump into the source repo | `chezmoi cd` |
| Re-snapshot installed brews | `cd ~/dotfiles && brew bundle dump --force` |
| Push changes | `chezmoi cd && git add -A && git commit -m "…" && git push` |

## Layout

```
~/dotfiles/
├── Brewfile                                       # source of truth for brew packages
├── README.md
├── dot_zshrc                                      # → ~/.zshrc
├── dot_zprofile                                   # → ~/.zprofile
├── run_onchange_before_install-packages.sh.tmpl   # runs `brew bundle` on Brewfile change
└── examples/                                      # templates for the local-only files
    ├── work.zsh.example
    └── secrets.zsh.example
```

chezmoi only deploys files at the source root that start with `dot_` (or other
recognized prefixes). The `examples/` directory is reference material — chezmoi
ignores it.

## Local-only files

Two files are sourced by `~/.zshrc` if they exist, but live **outside** this repo
so that secrets and employer-specific aliases never land on GitHub:

| File | Purpose | Template |
| --- | --- | --- |
| `~/.config/zsh/work.zsh` | Aliases for current job / clients (project paths, framework shortcuts). | [`examples/work.zsh.example`](./examples/work.zsh.example) |
| `~/.config/zsh/secrets.zsh` | API tokens, env-var secrets. | [`examples/secrets.zsh.example`](./examples/secrets.zsh.example) |

Both files should be `chmod 600` and recreated by hand on each new machine.
For higher-stakes secrets, prefer a real secret store (1Password CLI, macOS
keychain, age/sops) over plaintext exports.

## Adding a new brew package

```sh
brew install <pkg>                                # install on this machine
cd ~/dotfiles && brew bundle dump --force         # update the Brewfile
git add Brewfile && git commit -m "add <pkg>" && git push
```

On any other machine, `chezmoi apply` will detect the Brewfile change and run
`brew bundle` automatically.
