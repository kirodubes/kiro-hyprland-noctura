# Changelog

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
- `../KIROTUX-PKG-BUILD/kiro-hyprland-noctura/PKGBUILD` (golden-copy path)
- `README.md`, `CLAUDE.md`
- removed `etc/skel/.config/kiro-hyprland-noctura/hyprland.lua.bak-before-dualws`
