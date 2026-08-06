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



## FreeBSD

This is the `freebsd` branch, for FreeBSD 15. It is the `master` rice with
every Linux-only assumption replaced by its base-system equivalent:

| Was | Is here |
| --- | --- |
| `/sys/class/power_supply`, `/sys/class/backlight` | `hw.acpi.battery.*` sysctls, `backlight(8)` |
| `/sys/class/net`, `/proc/net/*` | `ifconfig(8)`, `netstat -ibn` |
| `/proc/stat`, `free(1)` | `kern.cp_times`, `vm.stats.vm.*` sysctls |
| `/proc/PID/cwd`, `pstree(1)` | `procstat(1)`, `pgrep -P` |
| `pidof(1)` | `pgrep(1)` |
| `pacman`/`yay` | `pkg(8)` |
| `systemctl`/`loginctl` power actions | `shutdown(8)`, `acpiconf(8)` |
| `lsblk`, `cryptsetup`, `/etc/mtab` | `gpart(8)`, `fstyp(8)`, `geli(8)`, `mount -p` |
| `mount -t cifs` | `mount_smbfs(8)` |
| `numfmt`, `df --output`, `find -printf` | POSIX equivalents |
| ALSA (`-f alsa`) recording | OSS (`-f oss -i /dev/dsp`) |
| pipewire/`wpctl` volume, `pulsemixer` | OSS `mixer(8)`, `mixertui` |
| mpd `type "pipewire"` | mpd `type "oss"` on `/dev/dsp` |
| `tsp` (task spooler) | `/usr/local/bin/ts`, which is what `sysutils/ts` installs |
| `setsid -f` (util-linux) | `daemon -f` (base); `setsid -w -f` just runs in the foreground |
| `killall -q` | `pkill -x` |
| `/dev/tty1` autostart | `/dev/ttyv0` |

### Signalling the status bar

FreeBSD's `kill(1)` and `pkill(1)` only know the names of the signals up to
`SIGUSR2`, so `pkill -RTMIN+10 dwmblocks` - which is what the Linux scripts
use - is rejected here. The scripts send the number instead: `SIGRTMIN` is
**65** on FreeBSD, so module signal *n* is `pkill -$((65 + n))`, e.g. the
volume module (signal 10) is `pkill -75`. On Linux the same modules would be
34 + *n*.

The compositor runs on the `glx` backend, including in a virtual machine.
There is no hardware GL under VMware — `vmwgfx` does not exist on FreeBSD any
more, so there is no DRM device — but `graphics/mesa-dri` provides
`swrast_dri.so` and llvmpipe backs the context well enough.

It has to be glx: picom's `xrender` backend ignores `corner-radius` and draws
every window square ([picom #683][picom683]). That is a missing feature rather
than a quality difference, so xrender is only a fallback for a machine with no
usable GL at all. What made this hard to place is that `xprofile` used to send
picom's output to `/dev/null`; it logs to `/tmp/picom.log` now.

Screen blanking is turned off in a virtual machine. That is what used to cost
glx its context — picom survived startup and then stopped painting after a
DPMS wake, leaving a desktop that looked frozen while X and dwm were still
running — and a virtual screen has no power to save anyway.

[picom683]: https://github.com/yshui/picom/issues/683

Audio is the base system's OSS throughout: no sound server, nothing to
autostart, and the kernel mixes the streams itself through vchans. `sb-volume`
and `sysact` drive `mixer(8)` directly, but both still defer to `wpctl` if
they find a pipewire session running, since pipewire is in the ports tree for
anyone who wants it. Deploy it with the `freebsd` branch of
[bootstrap](https://github.com/james5618/bootstrap), which installs the
matching `freebsd` branches of dwm, dwmblocks, st and dmenu.
