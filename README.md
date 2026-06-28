# omarchy-i18n

Translate Omarchy's menus, clock format, keyboard layout, and system locale.
No changes to Omarchy itself are required — everything is done through
configuration files and an injected menu extension.

## What it does

When you run `omarchy-i18n`, an interactive assistant walks you through:

1. **Language** — sets your system locale (dates, numbers, language-dependent apps)
2. **Clock** — choose 12h or 24h format for Waybar and the system
3. **Keyboard** — optionally change your kb layout (US is kept by default)
4. **Translation** — scans Omarchy's menu source for every string and replaces
   it with the translated version. This works by generating a small extension
   script at `~/.config/omarchy/extensions/menu.sh` that Omarchy sources at
   runtime — the original binary is never touched.

After `omarchy update`, new menu strings may appear. Run `omarchy-i18n --check`
to detect them, or enable the optional systemd timer for passive monitoring.

## How it works

Rather than patching Omarchy's binary, `omarchy-i18n` uses Omarchy's own
extension mechanism: the generated `menu.sh` overrides the displayed labels
at runtime. An interactive `translate()` function in that extension first
looks up full phrases in a JSON dictionary; if a phrase isn't found, it
falls back to word-by-word translation using a separate word dictionary.

Translation data lives in `~/.config/omarchy/i18n/<lang>.json` — separate
from the script itself. To fix a missing translation, you edit the JSON file,
not the script.

## Installation

Clone the repo and copy the files to their right places.

```bash
git clone https://github.com/ElMeGGa-14/omarchy-i18n.git
cd omarchy-i18n
```

Copy the main script:

```bash
cp bin/omarchy-i18n ~/.local/bin/
chmod +x ~/.local/bin/omarchy-i18n
```

Copy the translation dictionaries:

```bash
mkdir -p ~/.config/omarchy/i18n
cp i18n/*.json ~/.config/omarchy/i18n/
```

Optionally enable the daily check for new strings:

```bash
cp systemd/* ~/.config/systemd/user/
systemctl --user daemon-reload
systemctl --user enable --now omarchy-i18n-check.timer
```

## Usage

Each line is a separate command — run only what you need.

```bash
omarchy-i18n
```
Run the interactive setup wizard (language, clock, keyboard, translation).

```bash
omarchy-i18n --check
```
Scan for any new strings added by a recent `omarchy update` that still need translation.

```bash
omarchy-i18n --watch
```
Silent check — no output unless new untranslated strings are found. Used by the systemd timer.

```bash
omarchy-i18n --install-watch
```
(Re)generate the systemd timer units in `~/.config/systemd/user/`.

## Supported languages

| Code | Language |
|------|----------|
| es   | Spanish  |
| fr   | French   |
| it   | Italian  |
| pt   | Portuguese |
| ru   | Russian  |
| zh   | Chinese  |

## Adding a new language

Create `~/.config/omarchy/i18n/<code>.json` using the same structure as the
existing files:

```json
{
  "phrases": { "Full menu string": "Translated string" },
  "words":   { "word": "translated" }
}
```

`phrases` is checked first. If a menu string isn't listed there, the script
splits it into words and translates each one via `words`. Unknown words are
left in English. No script editing required.

## Technical notes

- All modified configs (Waybar, Mako, Walker, Hyprland) are backed up with a
  `.bak.<timestamp>` suffix before changes are applied.
- `sudo locale-gen` is used to generate the system locale — the script passes
  the password via stdin (it prompts you once at the start).
- The systemd timer runs `omarchy-i18n --watch` daily. If new untranslated
  strings are found, it sends a desktop notification via `notify-send`.
- Running `omarchy refresh` for Waybar or Hyprland will undo some changes;
  just re-run `omarchy-i18n` to restore them.
