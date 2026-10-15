# Honor Earbuds Plugin for Noctalia Shell v5

[🇬🇧 English](README.md) | [🇷🇺 Русский](README.ru.md)

Integration of **HONOR CHOICE** series TWS earbuds into [Noctalia Shell v5](https://noctalia.dev).

This plugin provides Active Noise Cancellation (ANC) control, real-time battery monitoring for each earbud and the charging case, and connection toast notifications.

---

## 🎧 Supported Models

The plugin communicates with the earbuds using the **Bestechnic (BES) RFCOMM SPP** protocol (channels 18 / 12) — the same protocol utilized by the mobile **Honor AI Space** app:

| Model | Support Status | Notes |
|---|---|---|
| **HONOR CHOICE Earbuds X5** | 🟢 **100% Tested** | ANC (Noise Cancellation / Awareness / Off), accurate battery (L/R/Case) |
| **HONOR CHOICE Earbuds X5 Pro** | 🟢 **Full Compatibility** | ANC, battery, hardware EQ |
| **HONOR CHOICE Earbuds X3 / X3 Lite** | 🟢 **Full Compatibility** | ANC, battery |
| **HONOR CHOICE Earbuds X / X2** | 🟢 **Compatible** | Battery, playback controls |
| **Moecen TWS (OEM Honor Choice)** | 🟢 **Compatible** | Standard Bestechnic protocol |

---

## ✨ Features

- **Status Bar Widget:**
  - Dynamic headphones glyph and average battery level percentage.
  - Informative tooltip showing connection details and click hints.
  - Option to hide the widget when earbuds are disconnected.
- **Interactive Controls:**
  - **Left-Click** on widget — open dropdown control panel.
  - **Right-Click** on widget — cycle ANC modes:  
    `Noise Cancellation (ANC)` ➔ `Awareness (Transparency)` ➔ `Off (Normal)`.
- **Dropdown Panel:**
  - Independent battery progress bars for **Left**, **Right** earbuds, and the **Case**.
  - Low battery warning indication (highlighted in red when ≤ 20%).
  - Quick-switch buttons for ANC modes with visual highlighting of the active mode.
  - Manual refresh button.
- **Toast Notifications (OSD):**
  - Automatic popup notification upon connecting earbuds with current battery levels.
  - OSD alert when switching ANC modes.
- **Fast Backend:**
  - Ultra-fast connection state checks via `bluetoothctl` (~7 ms) preventing socket timeouts and system freezes when earbuds are powered off.
- **Standalone CLI Utility (`honor-anc`):**
  - Terminal control and keybind integration for Hyprland, Sway, and script automation.

---

## 📁 Plugin Structure

```text
honor-earbuds/
├── plugin.toml            # Plugin manifest, declared settings, panel and widget geometry
├── widget.luau            # Status bar widget for Noctalia
├── panel.luau             # Dropdown control panel & battery monitor
├── service.luau           # Background daemon: state polling, IPC dispatch, notifications
├── scripts/
│   ├── honor_ctl.py       # Fast JSON controller (RFCOMM + bluetoothctl)
│   └── honor-earbuds.py   # Standalone CLI honor-anc tool
└── translations/
    ├── ru.json            # Russian localization
    └── en.json            # English localization
```

---

## ⚙️ Settings

Settings are accessible through Noctalia Shell's graphical settings window or can be configured directly:

| Setting | Key | Type | Default | Description |
|---|---|---|---|---|
| **Notify on Connect** | `notify_on_connect` | `bool` | `true` | Show popup notification with battery status upon connection |
| **Hide When Disconnected** | `hide_when_disconnected` | `bool` | `false` | Hide widget from the bar when earbuds are disconnected |
| **Device MAC Address** | `device_mac` | `string` | `""` | Override Bluetooth MAC address (automatically detected by default) |

---

## ⌨️ IPC Commands & Keybindings

You can trigger plugin actions from a terminal, scripts, or bind them to global shortcuts in your window manager (e.g., Hyprland):

```bash
# Cycle ANC mode (Off -> ANC -> Awareness -> Off)
noctalia msg plugin nezerkill/honor-earbuds:service all cycle-noise

# Set a specific ANC mode (anc, transparency, off)
noctalia msg plugin nezerkill/honor-earbuds:service all set-noise anc

# Trigger battery status notification
noctalia msg plugin nezerkill/honor-earbuds:service all notify-battery

# Toggle dropdown control panel
noctalia msg panel-toggle nezerkill/honor-earbuds:panel
```

### Example for `hyprland.conf`:

```ini
# Cycle noise cancellation with Super + Alt + N
bind = $mainMod ALT, N, exec, noctalia msg plugin nezerkill/honor-earbuds:service all cycle-noise

# Show earbud battery toast with Super + Alt + B
bind = $mainMod ALT, B, exec, noctalia msg plugin nezerkill/honor-earbuds:service all notify-battery
```

---

## 🛠️ Standalone CLI Utility `honor-anc`

The package also includes `scripts/honor-earbuds.py` (symlinked to `~/.local/bin/honor-anc`), which functions independently of any graphical shell:

```bash
# Check connection status, ANC mode, and battery levels
honor-anc status

# Cycle ANC mode
honor-anc cycle

# Set a specific mode
honor-anc set anc
honor-anc set awareness
honor-anc set off

# Get battery status in JSON format
honor-anc battery --json
```

---

## 📜 License

MIT License.
