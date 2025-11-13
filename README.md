# Chrome Download Auto-Resume (CDAR)

<div align="center">

![Platform](https://img.shields.io/badge/platform-Windows-lightgrey.svg)
![Chrome](https://img.shields.io/badge/browser-Google%20Chrome-blue.svg)
![License](https://img.shields.io/badge/license-MIT-green.svg)

**Automatically resumes interrupted or paused downloads in Google Chrome**

[Features](#-features) • [Download](#-download) • [How to Use](#-how-to-use) • [Configuration](#️-configuration) • [FAQ](#-faq)

</div>

---

## 📋 About

Chrome Download Auto-Resume (CDAR) is a Windows desktop application that automatically resumes Chrome downloads that failed due to network errors or were manually paused. It works by detecting the Chrome downloads window and automatically clicking the "Resume" buttons through UI automation.

### 🎯 Problem It Solves

When you have multiple large downloads that constantly fail due to connection issues, manually clicking the "Resume" button for each one becomes tedious. CDAR automates this process by running periodic cycles to resume all interrupted downloads.

### ❓ Why Not a Chrome Extension?

Although a Chrome extension seems like the obvious solution, there are **important technical limitations**:

1. **Limited Downloads API**: The `chrome.downloads` API does not allow resuming downloads that failed due to network errors. It only works for manually paused downloads.

2. **No UI Access**: Extensions cannot interact with the `chrome://downloads/` page for security reasons. Chrome blocks extension scripts on internal pages.

3. **Manifest V3 Restrictions**: Chrome's new Manifest V3 imposes even more restrictions on permissions and API access.

4. **UI Automation Required**: The only reliable way to resume failed downloads is through UI automation, simulating clicks on the "More actions" → "Resume" buttons.

That's why a **desktop application using UI automation** is the most effective solution for this specific problem.

---

## ✨ Features

- ✅ **Automatic Resumption**: Detects and resumes downloads with "Failed - Network error" status
- ✅ **Paused Downloads**: Optionally resumes manually paused downloads
- ✅ **Periodic Execution**: Runs automatically at configurable intervals (default: 30 minutes)
- ✅ **Smart Scrolling**: Detects downloads outside the visible area and scrolls automatically
- ✅ **Precise Detection**: Uses proximity to find the correct "More actions" button
- ✅ **Debug Mode**: Visual red circle for debugging (shows where it will click)
- ✅ **Protections**: Validates windows to avoid clicking in wrong places
- ✅ **CLI Parameters**: Flexible configuration via command line
- ✅ **Detailed Logs**: Clear information about what's happening

---

## 📥 Download

Download the latest version from the [Releases](https://github.com/CRM2007/Chrome-Download-Auto-Resume-CDAR/releases) page:

- **CDAR.exe** - Standalone executable (no installation required)

### Requirements

- **Windows** (7/8/10/11)
- **Google Chrome** installed

---

## 💻 How to Use

### Basic Execution

1. **Download** the `CDAR.exe` file
2. **Double-click** to run (no installation needed)
3. The program will:
   - Detect or open the Chrome Downloads window
   - Wait 5 seconds for the interface to load
   - Search for failed/paused downloads
   - Click "More actions" → "Resume" for each one
   - Wait 30 minutes and repeat indefinitely

### Stopping the Program

Press `Ctrl+C` in the console window to stop the program at any time.

---

## ⚙️ Configuration

### Command Line Parameters

You can customize CDAR's behavior using command-line parameters:

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `--n-clicks` | int | 10 | Number of downloads to resume per cycle (1-100) |
| `--interval` | int | 30 | Interval between cycles in minutes (1-1440) |
| `--enable-paused` | bool | false | Resume paused downloads in addition to failed ones |
| `--debug` | bool | false | Show red debug circle |
| `--help` | - | - | Show complete help |

### Accepted Boolean Values

- **TRUE**: `true`, `t`, `1`, `yes`, `y` (case-insensitive)
- **FALSE**: `false`, `f`, `0`, `no`, `n` (case-insensitive)

### Usage Examples

#### Using Command Prompt (cmd)

```cmd
REM Run with 60-minute interval
CDAR.exe --interval 60

REM Resume 20 downloads per cycle, including paused ones
CDAR.exe --n-clicks 20 --enable-paused true

REM Debug mode active + custom interval
CDAR.exe --debug true --interval 45

REM Complete configuration
CDAR.exe --n-clicks 15 --interval 120 --enable-paused true --debug false
```

#### Creating a Shortcut with Parameters

1. Right-click `CDAR.exe` → **Create shortcut**
2. Right-click the shortcut → **Properties**
3. In the **Target** field, add parameters after the .exe path:
   ```
   C:\path\to\CDAR.exe --interval 60 --enable-paused true
   ```
4. Click **OK** and use the shortcut to run with your custom settings

#### Start with Windows

To run CDAR automatically when Windows starts:

1. Create a shortcut with your desired parameters (see above)
2. Press `Win+R`, type `shell:startup`, and press Enter
3. Copy your shortcut to the Startup folder that opens
4. CDAR will now run automatically on Windows startup

---

## 🔧 How It Works

### 1. Download Window Detection

CDAR uses **UI Automation** to:
- Search for Chrome window with title containing "download"
- If not found, automatically opens it via `Ctrl+J` or new window
- Validates that it's actually the downloads window (not other apps)

### 2. Searching for Interrupted Downloads

The program looks for downloads with specific statuses:
- **Failed downloads**: "failed" + "network error" in text
- **Paused downloads**: "paused" in text (if enabled via `--enable-paused`)

### 3. Automatic Scrolling

For downloads outside the visible area:
- Brings element into viewport
- Moves mouse over item to reveal hidden buttons

### 4. Intelligent Button Detection

Uses **spatial proximity** to find the correct "More actions" button:
- Calculates distance between download item and button
- Selects the closest button (within 100px)
- Filters small buttons (45x45px) from larger container elements

### 5. Clicking "Resume"

1. Moves mouse over item (reveals hidden buttons)
2. Clicks "More actions" button (three dots)
3. Waits for menu to open
4. Clicks "Resume" in dropdown menu

---

## 🎮 Debug Mode

Enable debug mode to visualize where the script will click:

```cmd
CDAR.exe --debug true
```

A **red circle** will appear on screen for 5 seconds before each click, showing exactly where the "More actions" button was detected.

---

## 📊 Example Logs

```
============================================================
 Chrome Download Auto-Resume (CDAR)
============================================================
 Settings:
   - Interval: 30 minutes
   - Failed downloads: ENABLED
   - Paused downloads: DISABLED
   - Max items per cycle: 10
   - Debug visual: DISABLED
============================================================

>>> Cycle #1

============================================================
[CYCLE] Starting resume cycle - 2025-01-10 15:30:00
============================================================
Searching for Chrome windows...
Found 5 window(s) with class_name Chrome_WidgetWin_1
[OK] Found Downloads window: Download history - Google Chrome
[VALIDATED] Target window: Download history - Google Chrome
Waiting 5 seconds for interface to load...
Starting clicks.

[UIA] Searching for failed downloads to resume...
[UIA] Found failed download: file.zip failed - network error...
[UIA] Total downloads to resume found: 1

[UIA] Scrolling to make item visible...
[UIA] Moving mouse over item to reveal buttons...
[UIA] Found 'More actions' 15px from item, clicking...
[UIA] Found 'Resume' in menu, clicking...
[UIA] Click #1 successful

UIA clicks: 1
[CYCLE] Completed - Total downloads resumed: 1

[WAITING] Next cycle in 30 minutes...
[INFO] Next cycle at: 16:00:00
```

---

## 🛡️ Implemented Protections

1. **Window Validation**: Verifies it's actually the Chrome Downloads window
2. **VS Code Filter**: Ignores VS Code windows that also use `Chrome_WidgetWin_1`
3. **Focus Verification**: Confirms correct window is in focus before clicking
4. **Relative Coordinates**: Calculates coordinates relative to window position
5. **Spatial Proximity**: Selects button closest to item (avoids wrong button)
6. **Parameter Validation**: Discards invalid values and uses defaults

---

## 🐛 Troubleshooting

### Problem: "Could not find Downloads window"
**Solution**: Manually open the `chrome://downloads/` page and run CDAR again.

### Problem: Clicks in wrong place
**Solution**:
1. Enable debug mode: `CDAR.exe --debug true`
2. Observe where the red circle appears
3. If issue persists, please report on GitHub Issues

### Problem: Paused downloads are not resumed
**Solution**: Use `--enable-paused true` to enable resuming paused downloads.

### Problem: Program doesn't start
**Solution**:
- Make sure Google Chrome is installed
- Run as Administrator if needed
- Check Windows Defender/Antivirus isn't blocking it

---

## 🤝 Contributing

Contributions are welcome! Feel free to:

1. Fork the project
2. Create a branch for your feature (`git checkout -b feature/AmazingFeature`)
3. Commit your changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

---

## 📝 License

This project is licensed under the MIT License. See the `LICENSE` file for details.

---

## 🙏 Acknowledgments

- [pywinauto](https://github.com/pywinauto/pywinauto) - Windows UI automation library
- Python community

---

## 📈 Roadmap

- [ ] Support for other browsers (Firefox, Edge)
- [ ] Optional graphical interface (GUI)
- [ ] Desktop notifications when downloads are resumed
- [ ] Download statistics
- [ ] File logging

---

## ❓ FAQ

### Is this safe to use?
Yes! CDAR only interacts with the Chrome downloads page and does not access your files, passwords, or any other data. It simply simulates clicking the "Resume" button.

### Does it work with other browsers?
Currently, CDAR only supports Google Chrome. Support for other browsers may be added in the future.

### Can I run it in the background?
Yes! You can minimize the console window and CDAR will continue running. To hide it completely, you can use tools like [NirCmd](https://www.nirsoft.net/utils/nircmd.html) to run it hidden.

### How much CPU/RAM does it use?
Very little! CDAR is lightweight and only uses resources when actively checking for downloads (a few seconds every 30 minutes by default).

### Will it resume downloads from Chrome's built-in download manager?
Yes! CDAR works with Chrome's native download manager (`chrome://downloads/`).

### Can I use it for torrents or download managers?
No, CDAR is specifically designed for Google Chrome's built-in download manager. It will not work with external download managers or torrent clients.

---

## 📸 Screenshots

> **Note:** The program logs are currently in Portuguese. If 10+ people actively use this tool, I'll translate everything to English!

<img width="975" height="1377" alt="image" src="https://github.com/user-attachments/assets/611b2541-0d79-441d-887b-8298bbfdbe37" />
<img width="1802" height="801" alt="image" src="https://github.com/user-attachments/assets/c9e9fc3d-5b5a-4e6f-bd78-56a2346de73d" />

---

<div align="center">

**⭐ If this project helped you, leave a star! ⭐**

[Report Bug](https://github.com/CRM2007/Chrome-Download-Auto-Resume-CDAR/issues) • [Request Feature](https://github.com/CRM2007/Chrome-Download-Auto-Resume-CDAR/issues)

</div>

