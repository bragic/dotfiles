# dotfiles

My terminal environment for Kubernetes training and day-to-day infrastructure work on macOS. Everything here is managed with [GNU Stow](https://www.gnu.org/software/stow/) so a fresh machine can be rebuilt in a few minutes.

<!-- TODO: add a screenshot of the Starship prompt, e.g. docs/prompt.png -->
![Terminal prompt](docs/prompt.png)

## What's in here

| Package | Contents |
|---|---|
| `bash/` | `.bash_profile` (Homebrew shellenv, PATH, `EDITOR`/`VISUAL`) and `.bashrc` (Starship init, kubectl aliases) |
| `alacritty/` | `~/.config/alacritty/alacritty.toml` and the Catppuccin Mocha theme |
| `starship/` | `~/.config/starship.toml` using the gruvbox-rainbow preset |
| `Brewfile` | Tools installed through Homebrew |

## The stack

- **Terminal:** [Alacritty](https://alacritty.org/) with the Catppuccin Mocha theme and a Nerd Font for powerline glyphs
- **Shell:** bash, launched as a login shell by Alacritty
- **Prompt:** [Starship](https://starship.rs/) with the gruvbox-rainbow preset
- **Editor:** Neovim, exposed as `vim` through a symlink so it works in scripts and under `sudo`, not only in interactive shells
- **Kubernetes:** `kubectl` aliases and `kubectx`

## Restore on a new Mac

```sh
# 1. Install Homebrew first: https://brew.sh
git clone https://github.com/<your-username>/dotfiles.git ~/dotfiles
cd ~/dotfiles

# 2. Install everything in the Brewfile
brew bundle

# 3. Symlink the configs into place
stow bash alacritty starship

# 4. Make vim launch Neovim everywhere
ln -s /opt/homebrew/bin/nvim /opt/homebrew/bin/vim
```

Open a new Alacritty window and check:

```sh
echo $0                  # -bash
echo $EDITOR             # nvim
echo $STARSHIP_SHELL     # bash
vim --version | head -1  # NVIM
```

If `stow` reports a conflict, a real file already exists at the target path. Move it aside (or back it up) and run `stow` again.

## Design decisions

**Stow over a hand-rolled install script.** Each directory is a "package" that mirrors the layout under `$HOME`. `stow <package>` creates the symlinks and `stow -D <package>` removes them. Editing a file in `~` edits the file in the repo, so changes never drift.

**Symlink over alias for `vim`.** An alias is a text substitution inside one interactive shell. A symlink in a directory that comes first on `PATH` is visible to scripts, `sudo`, cron, and other programs that launch an editor.

**bash as the login shell for Alacritty.** Set explicitly in `alacritty.toml`. A login bash reads `~/.bash_profile`, not `~/.bashrc`, so `.bash_profile` sources `.bashrc` to keep one place for interactive settings.

**Verify with commands, not assumptions.** `$SHELL` reports the account's login shell, not the shell that is running. Use `echo $0` and `$BASH_VERSION` to see what is actually in use.

## What is deliberately not here

- Credentials and tokens: `~/.kube`, `~/.azure`, `~/.ssh`, and anything in `~/.secrets`
- The Rancher Desktop block in `.bash_profile`, which the app manages and rewrites
- Machine-specific paths and settings

Secrets, when needed, live in `~/.secrets`, which `.bashrc` sources if it exists. That file is listed in `.gitignore`.

## Notes

- Apple's bundled bash is 3.2. Homebrew bash 5.x is in the Brewfile and can be used by pointing `[terminal.shell] program` in `alacritty.toml` at `/opt/homebrew/bin/bash`. Modern kubectl tab completion needs bash 4+ and `bash-completion@2`.
- Tested on Apple Silicon (Homebrew prefix `/opt/homebrew`). On Intel Macs, replace that prefix with `/usr/local`.

## License

MIT

