# dotfiles

My dotfiles, deployed by my [bootstrap](https://github.com/james5618/bootstrap)
script and designed around my builds of:

- [dwm](https://github.com/james5618/dwm) (window manager)
- [dwmblocks](https://github.com/james5618/dwmblocks) (statusbar)
- [st](https://github.com/james5618/st) (terminal emulator)
- [dmenu](https://github.com/james5618/dmenu) (launcher)


## Theming

The desktop is themed centrally through Xresources:

- `~/.config/x11/xresources` holds the active palette (Catppuccin); dwm, st
  and dmenu all read it at startup, and Super+F5 reloads it live.
- `~/.config/picom/picom.conf` sets up the picom compositor: rounded corners,
  shadows and vsync, with `unredir-if-possible` so fullscreen games bypass
  compositing entirely.
- JetBrainsMono Nerd Font is the interface font, set in
  `~/.config/fontconfig/fonts.conf`.

## Layout

- Useful scripts are in `~/.local/bin/`
- Settings for nvim, zsh, lf, mpd/ncmpcpp, nsxiv, mpv, dunst and more
- Configs stay in `~/.config/` where possible; `~/.zprofile` moves the rest
  there via environment variables
- File/directory bookmarks in `~/.config/shell/bm-files` and `bm-dirs`

## Install

Use [bootstrap](https://github.com/james5618/bootstrap) to autoinstall
everything, or clone this repo into your home directory and install the
[dependencies](https://github.com/james5618/bootstrap/blob/master/progs.csv).


