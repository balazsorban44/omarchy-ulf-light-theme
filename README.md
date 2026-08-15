# ulf-light — the light half of a monochrome orange Omarchy theme

The light twin of [**ulf**](https://github.com/balazsorban44/omarchy-ulf-theme).
Same [omarchyplugins.com](https://omarchyplugins.com) origin, same sharp
corners, same accent `#ff5a36` — on a warm near-white ground.

> **Dark variant:** [balazsorban44/omarchy-ulf-theme](https://github.com/balazsorban44/omarchy-ulf-theme)
> Install both and the appearance hook below switches the whole desktop between them.

![ulf-light](screenshots/ulf-light.png)

Every chromatic slot lives in OKLCH hue **22–108**. There is no blue, no green
and no magenta — only the warm band. The named ANSI slots stay because they are
positional, not descriptive: apps ask for "colour 4" and only need it reliably
distinguishable, so `blue` and `cyan` become the low-chroma ash tier.

| slot | ulf-light | ulf (dark) |
|---|---|---|
| background | `#faf6f3` | `#0d0b09` |
| foreground | `#221c18` | `#d7d3d0` |
| accent | `#ff5a36` | `#ff5a36` |
| red | `#720000` | `#dc4506` |
| green | `#7e2913` | `#d57059` |
| yellow | `#8b6000` | `#ffb000` |
| blue | `#3c342b` | `#7d7969` |
| magenta | `#ad4e44` | `#f06400` |
| cyan | `#564241` | `#b7a993` |

**This is not the dark palette recoloured.** On a near-white ground every slot
has to be dark to stay legible, which caps them below L≈0.55 and pushes the
whole palette into rust and terracotta. The two variants share only the accent
and the hue band.

## Install

```bash
omarchy theme install git@github.com:balazsorban44/omarchy-ulf-light-theme.git
```

Omarchy strips `omarchy-` and `-theme`, so this lands as `ulf-light`.

### The appearance hook

`omarchy theme set` does not touch GTK or gsettings, so switching between the
variants would leave the desktop signalling the wrong mode. Install the hook
once (it ships in both repos, identical) and light/dark follows the theme:

```bash
omarchy hook install theme-set hooks/theme-set.d/gtk-appearance
```

Four places have to agree, and fixing one does not fix the others:

| signal | read by |
|---|---|
| `gsettings color-scheme` | libadwaita, some GTK4 |
| `gsettings gtk-theme` | GTK3 |
| `gtk-{3,4}.0/gtk.css` | every GTK app — **overrides the two above** |
| `gtk-application-prefer-dark-theme` | Chromium's system-theme path only |

Each theme ships its own `gtk.css`; the hook swaps it on every theme change. A
stale one left over from the other variant is exactly what makes apps render
dark under the light theme.

## How the palette was derived

In a monochrome palette hue carries no information, so lightness and chroma do
all the separating — which makes slot placement a constraint problem rather
than something to pick by eye. `tools/palette-search-light.py` runs a
randomised search plus hill-climbing that maximises the worst of **all 91
non-twin pairs**, scored in OKLab under normal, protanopic and deuteranopic
vision, subject to:

- bright twins need a lightness step ≥ 0.06
- base ANSI slots must clear WCAG AA against the background; bright twins and
  decorative slots clear AA-large
- `green` is pinned to hue 26–52 so it sits beside the accent
- the *measured* hue is checked, not the requested one

Worst pair: **0.0423** (dark scores 0.0526 — it has far more lightness range to
work with). Relaxing the contrast floor to 4.0, where flexoki-light and
catppuccin-latte actually sit, scored *worse* at 0.0344 and turned `red` into a
brown-gold, so the stricter version was kept.

**Do not hand-edit a colour.** Change the constraints and re-run the search.

## Regenerating

Rendered by [Aether](https://github.com/omacom-io/aether) from
`source-colors.toml`, which it must fetch over http:

```bash
cd <dir containing source-colors.toml> && python3 -m http.server 8731 &
aether --handle-url 'aether://apply?colors=http://127.0.0.1:8731/source-colors.toml&as_omarchy_theme=ulf-light&silent=true'
tools/regenerate-patch.sh
```

Aether owns every file it renders, so `tools/regenerate-patch.sh` replays the
hand corrections — chiefly forcing the window border, hyprlock ring, mako
border and icon theme back to the orange accent. Aether picks **ANSI blue** for
borders, which in this palette is a near-neutral, so without the patch the
orange border comes out grey.

Two Aether traps worth knowing:

1. It sometimes reports success while writing a *stale* palette. Read back
   `colors.toml` rather than trusting its output.
2. While its GUI is open it takes the Omarchy theme slot back and repoints the
   background symlink into `~/.config/aether/theme/`. Close it first.

## Credits

Palette extracted from [omarchyplugins.com](https://omarchyplugins.com).
Fonts referenced by the source design: JetBrains Mono and Inter.

The wallpaper is a [wallhaven](https://wallhaven.cc) image (`3kx5gd`), included
for convenience only — check its licence before redistributing.
