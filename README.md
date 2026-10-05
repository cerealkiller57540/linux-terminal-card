<div align="center">

<img src="https://raw.githubusercontent.com/cerealkiller57540/linux-terminal-card/main/images/logo.png" alt="Linux Terminal Card" width="480">

**A sci-fi CRT terminal for Home Assistant that shows the health of a Linux PC: CPU, GPU, load, RAM, disk, temperature, battery, network and pending updates, with a ghost cat called Glitch.**

[![HACS Custom][hacs-badge]][hacs-url]
[![Release][release-badge]][release-url]
[![Validate][validate-badge]][validate-url]
[![License: MIT][license-badge]][license-url]

[![Open your Home Assistant instance and open this repository in HACS.](https://my.home-assistant.io/badges/hacs_repository.svg)](https://my.home-assistant.io/redirect/hacs_repository/?owner=cerealkiller57540&repository=linux-terminal-card&category=plugin)

<img src="https://raw.githubusercontent.com/cerealkiller57540/linux-terminal-card/main/images/main.png" alt="A CRT terminal with cyan text: OS and kernel, uptime, seven bars for CPU, GPU, load, RAM, disk, temperature and battery, network speed and a user@linux prompt. A pink-and-blue ghost cat flickers over the screen." width="560">

</div>

The card is a small shell session: an `OS … · k6.8` line, an `up …` line, a bar per metric and a prompt with a blinking cursor. The WebGL variant draws it on a real bulging tube (barrel distortion, chromatic aberration, scanlines, bloom, RGB phosphor mask and trails), inside a bezel plate. The CSS variant fakes the same look without WebGL.

Warnings are graded: an amber or red bar, then a red `CRITICAL` banner (`CRITIQUE` in French) and a red frame when temperature or battery crosses its critical threshold. When both CPU and RAM become unavailable, the screen turns into a dead link: `SIGNAL LOST`, `NO CARRIER`.

<div align="center">

<img src="https://raw.githubusercontent.com/cerealkiller57540/linux-terminal-card/main/images/alert.png" alt="The same terminal in alert: red banner, red CPU, temperature and battery bars, amber RAM bar, reboot required" width="400">
<img src="https://raw.githubusercontent.com/cerealkiller57540/linux-terminal-card/main/images/offline.png" alt="The terminal when the PC is unreachable: green text smeared by noise, RGB split, SIGNAL LOST and NO CARRIER" width="400">

*Left: a critical alert. Right: the PC is unreachable. Screenshots taken on a dashboard with the Neo Tokyo theme, with made-up sensor values.*

<img src="https://raw.githubusercontent.com/cerealkiller57540/linux-terminal-card/main/images/glitch.gif" alt="Animated terminal: the ghost cat appears with an RGB split, the screen flickers and a glitch band sweeps across" width="520">

</div>

## ✨ Features

- **Two cards in one install**
  - `linux-terminal-card-webgl`: the real CRT tube, rendered by a WebGL shader (recommended).
  - `linux-terminal-card`: the same layout in plain CSS, with scanlines, glow and flicker. Pick it if a view already carries many WebGL cards.
- **Metrics**: CPU, GPU, load (as a percentage of your core count), RAM, disk, temperature, battery and a second battery, network down and up, process count.
- **System line** from sensors of your choice: OS, kernel, uptime, pending updates, release upgrade, reboot required.
- **Graded alerts**: each bar turns amber then red at its own thresholds; temperature or battery in critical state raises a banner and a red frame.
- **Dead-link screen** when CPU and RAM are both unavailable, fully tunable.
- **CRT controls** (WebGL): curvature, aberration, scanlines, bloom, flicker, RGB mask, power-on animation, random glitch bursts, phosphor persistence.
- **Glitch the cat**: a hologram-style pixel-art cat that shows up now and then, and invades the screen when things go critical.
- **Re-label any slot** (`cpu_label`, `proc_label`, …) and set its own thresholds, to show something other than the default metric.
- **Tap a line or a bar** to open that entity's more-info dialog.
- **Header** with icon, font, gradient, glow and flicker, in the same style as the other neon cards.
- **Visual editor** with collapsible panels, in English or French. The WebGL card falls back to the CSS rendering if the browser has no WebGL.

## 📦 Installation

### HACS (recommended)

1. Click the **Open in HACS** button above, or add this repository as a custom repository in HACS (category **Dashboard**): `https://github.com/cerealkiller57540/linux-terminal-card`.
2. Download **Linux Terminal Card**.
3. Reload your browser.

One resource is enough: `linux-terminal-card.js` loads `linux-terminal-card-webgl.js` from the same folder.

### Manual

1. Copy both files of [`dist/`](dist) to `config/www/linux-terminal-card/`.
2. Add a dashboard resource: URL `/local/linux-terminal-card/linux-terminal-card.js`, type **JavaScript module**.

## 🚀 Usage

The card only reads entities, so any sensors work. The [Glances](https://www.home-assistant.io/integrations/glances/) integration provides CPU, memory, disk, temperature, battery and network sensors out of the box. The OS, kernel, uptime and update lines need sensors from a small reporter of your own (MQTT, a shell command sensor…); leave them out and the lines simply disappear.

```yaml
type: custom:linux-terminal-card-webgl
header:
  title: Linux PC
  icon: mdi:laptop
host: user@linux
os_entity: sensor.linux_pc_os
kernel_entity: sensor.linux_pc_kernel
uptime_entity: sensor.linux_pc_uptime
updates_entity: sensor.linux_pc_apt_pending_upgrades
reboot_entity: binary_sensor.linux_pc_reboot_required
cpu_entity: sensor.linux_pc_cpu_usage
gpu_entity: sensor.linux_pc_gpu_usage
load_entity: sensor.linux_pc_load_1m
load_cores: 8
ram_entity: sensor.linux_pc_memory_usage
disk_entity: sensor.linux_pc_disk_usage
temp_entity: sensor.linux_pc_cpu_temperature
battery_entity: sensor.linux_pc_battery
net_rx_entity: sensor.linux_pc_network_rx
net_tx_entity: sensor.linux_pc_network_tx
```

Network values are shown as they come, labelled `Mbit/s`: give the card sensors already in Mbit/s.

Tuning the tube:

```yaml
crt_curve: 0.6        # 0 = flat screen filling the card
crt_scanlines: 0.45
crt_bloom: 0.5
crt_aberration: 1.8
crt_persist: 0.6      # phosphor trails, 0 = off
crt_boot: 0           # skip the power-on animation
```

Using a slot for something else, for example a second disk:

```yaml
disk_entity: sensor.linux_pc_data_disk_usage
disk_label: DATA
disk_warn: 85
disk_hot: 95
```

## ⚙️ Options

Every entity option is optional: a missing sensor hides its line. Options marked *WebGL* are ignored by the CSS card.

**Entities**

| Option | Description |
|---|---|
| `os_entity` / `kernel_entity` / `uptime_entity` | Text sensors for the `OS … · k…` and `up …` lines |
| `updates_entity` / `release_entity` / `reboot_entity` | Pending updates (number), release upgrade, reboot required (binary sensor) |
| `proc_entity` | Process count, shown on the uptime line |
| `cpu_entity` / `gpu_entity` / `ram_entity` / `disk_entity` | Percentages |
| `load_entity` / `load_cores` | Load average and your core count (default `8`), shown as a percentage |
| `temp_entity` | Temperature in °C |
| `battery_entity` / `battery_entity_2` | Battery percentages (*`battery_entity_2` WebGL*) |
| `net_rx_entity` / `net_tx_entity` | Network speed, in Mbit/s |

**Thresholds** (a bar goes amber at `warn`, red at `hot`)

| Option | Default | Description |
|---|---|---|
| `temp_warn` / `temp_crit` | `65` / `85` | Temperature, in °C |
| `batt_warn` / `batt_crit` | `25` / `10` | Battery, in % (inverted: low is bad) |
| `cpu_warn` / `cpu_hot` | `70` / `90` | *WebGL* |
| `gpu_warn` / `gpu_hot` | `70` / `90` | *WebGL* |
| `load_warn` / `load_hot` | `80` / `100` | *WebGL* |
| `ram_warn` / `ram_hot` | `75` / `92` | *WebGL* |
| `disk_warn` / `disk_hot` | `80` / `93` | *WebGL* |

**Labels** (*WebGL*): `cpu_label`, `gpu_label`, `load_label`, `ram_label`, `disk_label`, `temp_label`, `batt_label`, `batt2_label` (with `batt2_warn` / `batt2_crit`) and `proc_label` replace the text in front of a bar.

**Look**

| Option | Default | Description |
|---|---|---|
| `host` | `user@linux` | Text of the prompt |
| `header` | — | `title`, `icon`, `icon_color`, `icon_size`, `font`, `font_weight`, `color`, `glow`, `glow_color`, `glow_size`, `gradient` (`gradient_from` / `gradient_to`), `flicker` |
| `title` | — | Shortcut for `header.title` |
| `col_txt` / `col_dim` / `col_prompt` / `col_primary` | cyan and green defaults | Text colours (*WebGL*) |
| `col_up` / `col_down` / `col_warn` / `col_crit` | — | Network and alert colours (*WebGL*) |
| `col_frame` | theme accent | Colour of the thin frame around the screen (*WebGL*) |
| `screen_bg` / `color_bg` | dark neutral | Screen background |

**CRT** (*WebGL*)

| Option | Default | Description |
|---|---|---|
| `crt_curve` | `0` | Tube curvature, `0` = flat |
| `crt_aberration` | `1.8` | Chromatic aberration strength |
| `crt_scanlines` | `0.45` | Scanline strength |
| `crt_bloom` | `0.5` | Glow around bright text |
| `crt_flicker` | `0.04` | Brightness flicker |
| `crt_mask` | `0.35` | RGB aperture grille, `0` = off |
| `crt_glitch` | `0.7` | Glitch burst intensity, `0` = off |
| `crt_boot` | `1` | Power-on animation at mount, `0` = off |
| `crt_persist` | `0.6` | Phosphor persistence, `0` = off |

**Dead-link screen** (*WebGL*): `offline_dim` (`0.46`), `offline_warp` (`1`), `offline_noise` (`6`), `offline_burst` (`1.5`), `offline_period` (`8` s between bursts) and `offline_split` (`8.5` px of RGB split).

## ❓ FAQ

**The WebGL card shows the CSS look.** Your browser or WebView has no WebGL, so the card falls back to the CSS rendering. That is expected.

**Some cards go blank on my Android phone.** Android WebViews keep at most 8 WebGL contexts per page and drop the oldest one. This card uses one. If a view has many WebGL cards, use `linux-terminal-card` for some of them.

**Where do the OS, kernel and update lines come from?** From any sensors you point the options at. The author feeds them from a small script that publishes over MQTT; Glances covers the rest. Without them the lines are hidden.

**Which languages are supported?** English and French. The editor and the card texts follow your Home Assistant language: French if it is French, English otherwise. Reload the page after changing the language.

**Does it load anything from the internet?** Only the JetBrains Mono and Orbitron fonts, from Google Fonts. No data leaves your Home Assistant.

**Which theme is in the screenshots?** Neo Tokyo, the author's own dark theme (not published). The card works with any theme: the glow follows `--rgb-primary-color` when the theme sets it, otherwise it is cyan.

## 🙏 Credits

- The CRT look of the CSS card follows the [old-timey terminal](https://css-tricks.com/old-timey-terminal-styling/) technique from CSS-Tricks.
- The hologram glitch of the cat is inspired by the Johnny Silverhand apparitions in *Cyberpunk 2077*. The pixel-art cat is the author's own mascot.

## 🌃 More neon cards

This card is part of a family. See the full collection at [**Home-Assistant-Neon-Cards**](https://github.com/cerealkiller57540/Home-Assistant-Neon-Cards).

---

## 🐾 Support this project

If you enjoy these cards, please consider donating to **Quatre Pattes**, an animal rescue organization.

[![Sauver des animaux](https://img.shields.io/badge/🐾%20Sauver%20des%20animaux-Faire%20un%20don-ff69b4?style=for-the-badge)](https://don.quatre-pattes.org/s/?_jtsuid=70083177244599792679303)

> 💛 No need to support me — just help the animals. Thank you!

---

## 🤝 Contributing

1. Fork the repo
2. Create your branch: `git checkout -b feature/my-card`
3. Commit and push
4. Open a Pull Request

---

## 📄 License

[MIT License][license-url]

[hacs-badge]: https://img.shields.io/badge/HACS-Custom-orange.svg?style=for-the-badge
[hacs-url]: https://hacs.xyz
[release-badge]: https://img.shields.io/github/v/release/cerealkiller57540/linux-terminal-card?style=for-the-badge
[release-url]: https://github.com/cerealkiller57540/linux-terminal-card/releases
[validate-badge]: https://img.shields.io/github/actions/workflow/status/cerealkiller57540/linux-terminal-card/validate.yml?branch=main&label=HACS&style=for-the-badge
[validate-url]: https://github.com/cerealkiller57540/linux-terminal-card/actions/workflows/validate.yml
[license-badge]: https://img.shields.io/github/license/cerealkiller57540/linux-terminal-card?style=for-the-badge
[license-url]: LICENSE
