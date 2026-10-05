# Changelog

## 2026.10.05

### What Changed
- Keybindings now follow the active keyboard layout: `resolve_binds_by_sym = true` in the `input` block. With `us,be`
  (or `be,us`), Hyprland used to read every bind as if the first layout were active, so after Alt+Shift you typed
  AZERTY but Super+letter binds stayed on their QWERTY key positions. Now Super+A is the A printed on the key in
  whichever layout is active. Workspace binds use `code:` keys (physical positions) and are unchanged. Tested on a
  QEMU install of kiro-hyprland-dms.
- `hyprland.lua` now ends with an **appearance loader**: `pcall(require, "appearance")` loads `appearance.lua` from
  the same folder when it exists. The upcoming Kirotux Hyprland Premium app writes the user's look there (only the
  values they changed), so it never edits `hyprland.lua` itself. No file = this edition's own look, as before.
- **The keyboard layout now follows the installer.** `kb_layout` used to be hardcoded, so a user who picked German
  in Calamares still typed US/Belgian in Hyprland. `hyprland.lua` now reads `XKBLAYOUT` / `XKBVARIANT` from
  `/etc/vconsole.conf` (written by `kiro_final` from the installer's choice); without it (live ISO) the old value stays.
  Kirotux Hyprland Premium can still override it per user in `appearance.lua`.
- **Ctrl+Alt+H opens Kirotux Hyprland Premium** (theme, icons, cursor, font, window look, wallpaper, keyboard,
  presets). The key used to start `hyprland-tweak-tool`, which no ISO ships any more. This edition is used with and
  without the app: where it isn't installed, the key shows a notification "Get Kirotux Hyprland Premium … Comes with
  the KIROTUX ISOs." instead of doing nothing (only on the key press, never by itself).
- `import-gsettings.sh` (runs at every login) now keeps a **Light** choice: it went `prefer-dark` unconditionally, so
  choosing Light in the app was undone at the next login. It also copies the cursor size into gsettings.
- **Default apps follow Kirotux Hyprland Premium.** Right after the `local term/files/browser/editor` lines, before
  any key is bound, `hyprland.lua` reads `kirotux_apps.lua` (written by the app) and uses the terminal, file manager,
  browser and editor picked there. Without the file nothing changes.
- **Super+F1 opens the browser variable, Super+F2 the editor variable** (they were fixed to `firefox` / `code`), so
  they follow the chosen apps too. Ctrl+Alt+F stays Firefox, like every Ctrl+Alt+letter key.
- Added `local browser = "firefox"` (this edition had no browser variable).

### Technical Details
- Added after `kb_options`. Hyprland's default (`false`) resolves symbol binds against the first layout in `kb_layout`.
- Proven on a QEMU kiro-hyprland-dms install (Hyprland 0.56.2): `package.path` starts with the config folder, a
  second `hl.config` merges, every reload re-reads the file, a missing file is silent, and a broken one is reported
  while the rest of the config still loads (`pcall`). The loader must stay the last lines so the user's choices win.
- `installer_keyboard(fallback)` (Lua `io`) above `hl.config`; `kb_layout = kb_layout`, `kb_variant = kb_variant`.
  Checked against `XKBLAYOUT=be`, quoted multi-layout values with variants, an empty value and a missing file.
  On the QEMU dms install (installer: Belgian) Hyprland went from `us,be` to `be`, no config errors. Fallback here: `be,us`.
- `import-gsettings.sh`: `color-scheme` follows `gtk-application-prefer-dark-theme` in `gtk-4.0/settings.ini`
  (`false`/`0` = light, anything else or no file = dark, as before); `gtk-cursor-theme-size` → `cursor-size`.
  Both new reads are `|| true`-guarded for the script's `set -euo pipefail`. Tested on the QEMU dms install.
- Bind added after Ctrl+Alt+E (replacing the `hyprland-tweak-tool` line where it still existed): `sh -c 'command -v
  kirotux-hyprland-premium && exec kirotux-hyprland-premium; exec notify-send …'`. Tested on QEMU both ways (window
  opens; DMS shows the notification, text fits). `keybindings.txt` says it comes with the KIROTUX ISOs.
- `do local ok, apps = pcall(require, "kirotux_apps") ... end` after `local keybindings`; a missing file is silent.
  Tested on the QEMU dms install: Hyprland picked up `terminal = "alacritty", editor = "/usr/bin/subl"`, no errors.

### Files Modified
- `etc/skel/.config/kiro-hyprland-noctura/hyprland.lua`
- `etc/skel/.config/kiro-hyprland-noctura/hyprland-hq-dualscreen.lua`
- `etc/skel/.config/kiro-hyprland-noctura/scripts/import-gsettings.sh`
- `etc/skel/.config/kiro-hyprland-noctura/keybindings.txt`

## 2026.09.05

### What Changed
- **Print now leaves a file behind.** The screenshot binds ran `grim … | wl-copy`, which put the
  image on the clipboard and nowhere else — no file, no notification, so the key looked dead next to
  chadwm's scrot bind that writes into `~/Pictures`. Both binds now call the shared
  `kiro-screenshot region` / `kiro-screenshot screen`, which saves a timestamped PNG in
  `~/Pictures/Screenshots`, still copies to the clipboard, and notifies with a thumbnail.

### Technical Details
- The helper is `/usr/bin/kiro-screenshot`, shipped by `kiro-wayland-dotfiles` — already a dependency
  of this package. The same clipboard-only line had been copy-pasted into twelve editions, so it now
  lives in exactly one place instead of being fixed twelve times.
- `hyprland.lua` **and** `hyprland-hq-dualscreen.lua` — both carry the binds and must stay in lockstep.
  The separate `super+ctrl+Print` flameshot bind is untouched.
- **Build `kiro-wayland-dotfiles` first**: it provides the binary this edition's binds call.

### Files Modified
- `etc/skel/.config/kiro-hyprland-noctura/hyprland.lua`
- `etc/skel/.config/kiro-hyprland-noctura/hyprland-hq-dualscreen.lua`
- `etc/skel/.config/kiro-hyprland-noctura/keybindings.txt`

## 2026.07.09

### Forked from kiro-hyprland-noctalia; dual-monitor config + doc cleanup

**What Changed**
- Established `kiro-hyprland-noctura` as its own edition (Hyprland + noctalia-shell),
  forked from `kiro-hyprland-noctalia`. The config folder, session entry, wrapper
  and hooks were already renamed to the `noctura` namespace; this pass fixes the
  copy/pasted documentation and comments that still referred to `noctalia`.
- **Genericized the shipped monitor config for public release.** The config had
  been tailored to a specific two-screen workstation. Removed the hardcoded
  monitor **hardware serial numbers**, the fixed dual-screen positions, the
  browser-to-workspace pins, the browser autostarts, and a personal
  `~/.bin/kiro-website-serve.sh` autostart (which also leaked a home path). The
  shipped default is now a clean single-monitor setup (`output = ""`,
  `preferred`/`auto`, workspaces `1..10`).
- **Keyboard layout set to `be,us`** (Belgian default, US secondary) in both Lua
  configs — this is Erik's personal edition, so Belgian is primary; Alt+Shift
  toggles to US.
- **Golden-copy path fixed** to `/usr/share/kiro/kiro-hyprland-noctura/` in the
  PKGBUILD (was still pointing at the noctalia path). noctura is a **standalone
  personal edition** — the `conflicts=('kiro-hyprland-noctalia' 'kiro-noctalia')`
  is intentional (not meant to co-install with the noctalia editions).
- **Added the missing Variety keybinds** to match ohmychadwm: `ALT+Left`
  (previous), `ALT+Right` (next), `ALT+Up` (toggle pause), `ALT+Down` (resume) —
  alongside the existing `ALT+n/p/t/f`. Added a "5. Wallpaper (Variety)" section
  to `keybindings.txt` (was absent) and fixed its stale `noctalia` header. The
  ohmychadwm pywal-recolor variants (`ALT+SHIFT+…`) and the `SUPER+CTRL+Space`
  selector were **not** ported: noctura themes via noctalia/matugen (no pywal),
  and `SUPER+CTRL+Space` is already "swap with master".
- **Added a "Dual-monitor setup" tutorial to the README** walking users through
  `hyprctl monitors`, declaring two screens, the optional 10 + 10 workspace split
  (`SUPER` = left screen, `SUPER + ALT` = right), per-app workspace pinning, and
  reloading. A ready-to-uncomment dual-monitor template is left in `hyprland.lua`.
- **Shipped the real two-screen config as `hyprland-hq-dualscreen.lua`** — a
  complete, working dual-monitor example (10+10 workspaces, browser-to-workspace
  pins) that users can `cp` over `hyprland.lua` and adapt. Kept the real monitor
  `desc:` strings so it works as-is on matching hardware; the personal
  `~/.bin/kiro-website-serve.sh` autostart was stripped (home-path leak / dead
  script for other users).
- Re-enabled the archiso-gated Calamares installer autostart (it had been
  commented out on the source workstation), matching the noctalia sibling.

**Technical Details**
- `hyprland.lua`: header comment + `import-gsettings.sh` autostart path corrected
  `noctalia` → `noctura`; monitor block replaced with a single-monitor default
  plus a commented dual-monitor example; workspace keybind loop reverted to the
  single-screen `1..10` scheme (the 10 + 10 two-screen loop now lives in the
  README as an opt-in); browser window-rule pins + autostarts removed.
- README.md / CLAUDE.md: all references to *this package* renamed
  `noctalia` → `noctura`; "noctalia-shell" (the actual shell) left intact.
- Removed a stray `hyprland.lua.bak-before-dualws` backup file.
- `keybindings.txt` already documents the shipped single-screen `1..10` scheme —
  unchanged.

**Files Modified**
- `etc/skel/.config/kiro-hyprland-noctura/hyprland.lua`
- new `etc/skel/.config/kiro-hyprland-noctura/hyprland-hq-dualscreen.lua`
- `etc/skel/.config/kiro-hyprland-noctura/keybindings.txt` (Variety section)
- `../KIROTUX-PKG-BUILD/kiro-hyprland-noctura/PKGBUILD` (golden-copy path)
- `README.md`, `CLAUDE.md`
- removed `etc/skel/.config/kiro-hyprland-noctura/hyprland.lua.bak-before-dualws`
