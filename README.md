# 👏 Wake-Up Windows: Clap-Powered App Launcher

**Windows-only** voice and clap-controlled app launcher! Say a wake word like "blueberry", then use clap patterns to launch apps. Customized with launches for WhatsApp, Notion, Antigravity IDE, YouTube Music, and Google Calendar in Brave.

✅ **Automatically detects Windows** and uses the right commands. 100% offline with Porcupine for fast wake word detection.

## 🎬 How It Works

1. **Say wake word** (e.g., "blueberry") → Activates 5-second window.
2. **Double clap** 👏👏 → Launches configured apps (e.g., WhatsApp, Notion).
3. **Triple clap** 👏👏👏 (within 30 seconds after double) → Opens a URL/video (e.g., Instagram or YouTube).

## ✨ Features

- 🖥️ **Windows-Optimized**: Tailored commands and paths for seamless app launches.
- 🔌 **100% Offline**: Wake word and clap detection run locally—no internet.
- 🎯 **Smart Detection**: Auto-OS handling with full path examples for reliability.
- 🎤 **Fast & Accurate**: Porcupine engine for wake words; amplitude-based clap counting.
- 🎵 **Customizable**: Easy tweaks for apps, URLs, sensitivity, and wake words.
- 🔒 **Privacy-First**: No data sent anywhere—your audio stays on-device.

## 📋 Requirements

- **Python 3.9-3.12** (download from [python.org](https://www.python.org/downloads/)—avoid 3.13 on Windows due to dependency issues).
- Built-in microphone (or external USB mic).
- Free Porcupine API key from [console.picovoice.ai](https://console.picovoice.ai/) (signup takes 1 min, free tier sufficient).

## 🚀 Quick Start

### 1. Get API Key
Sign up at [console.picovoice.ai](https://console.picovoice.ai/) and copy your Access Key (long string)—keep it handy.

### 2. Install
```bash
git clone https://github.com/Shio242/wake-up-windows.git
cd wake-up-windows
python -m venv venv
venv\Scripts\activate
pip install -r requirements.txt  # Installs pyaudio, numpy, pvporcupine
```

If PyAudio fails: Ensure Python 3.11/3.12; try `pip install --upgrade pip wheel` then rerun.

### 3. Add Your API Key
Open `clap_launcher.py` in Notepad or VS Code. At line ~32:
```bash
PORCUPINE_ACCESS_KEY = "your-key-here"  # Paste your key from console.picovoice.ai
```
Save—don't commit the real key!

### 4. Configure Your Apps
The script uses Windows commands like `subprocess.Popen` for reliable launches. Customize in `launch_all_apps()` (~line 267). Examples (uncomment/edit for your paths—find via shortcut > Properties > Target):
```bash
if self.os_type == "Windows":
subprocess.Popen([r"C:\Users\YOURNAME\AppData\Local\WhatsApp\WhatsApp.exe"]) # for whatsapp desktop
subprocess.Popen([r"C:\Users\YOURNAME\AppData\Local\Programs\Notion\Notion.exe"]) # for notion desktop
subprocess.Popen([r"C:\Program Files\Google\Antigravity IDE\antigravity.exe"]) #Antigravity IDE (edit path)
subprocess.Popen([r"C:\Users\YOURNAME\AppData\Local\BraveSoftware\Brave-Browser\Application\brave.exe", "https://music.youtube.com/"]) #YouTube Music in Brave (edit path)
subprocess.Popen([r"C:\Users\YOURNAME\AppData\Local\BraveSoftware\Brave-Browser\Application\brave.exe", "https://calendar.google.com/"]) #Google Calendar in Brave (edit path)

#Add more: e.g., Discord
subprocess.Popen([r"C:\Users\YOURNAME\AppData\Local\Discord\app-1.0.9003\Discord.exe"])
```

For Store apps (e.g., WhatsApp): Use `os.system("start whatsapp:")` instead of Popen.

### 5. Run
```bash
python clap_launcher.py --wake blueberry
```
Output:
```bash
🖥️  Detected OS: Windows
📋 Available wake words: jarvis, computer, alexa...
✅ Wake word 'blueberry' loaded successfully!
🎧 Listening for 'blueberry'...
```

Say "blueberry" and clap—apps launch!

**One-Click Batch**: Create `start_wakeup.bat` in the folder:
```bash
@echo off
cd /d "%~dp0"
venv\Scripts\activate
python clap_launcher.py --wake blueberry
pause
```
Double-click for instant start.

## 🎮 Available Wake Words
- `jarvis` (original default)
- `computer`
- `alexa`
- `hey google`
- `hey siri`
- `ok google`
- `terminator`
- `bumblebee`
- `porcupine`
- `blueberry` (your default)
- `grapefruit`
- `grasshopper`

Use another: `python clap_launcher.py --wake computer`.

## 🛠️ Customization

### Change Triple Clap URL/Video
In `play_youtube_video()` (~line 300):
```bash
youtube_url = "https://www.instagram.com/p/DMZ58Whvfir/"  # Edit to your video/URL
```
Works for YouTube, Instagram, websites, etc. Triple clap opens it in default browser.

### Adjust Clap Sensitivity
In `__init__` (~line 50):
```bash
clap_threshold = 1800  # Lower for softer claps (e.g., 1500); higher for noisy rooms (e.g., 2200)
```
Run `--debug` to see amplitudes and tune.

### Add More Apps
In `launch_all_apps()`: Add lines like:
```bash
subprocess.Popen([r"C:\Path\To\Spotify\Spotify.exe"])  # Spotify
```
For URLs: `subprocess.Popen([r"C:\Path\To\Chrome\chrome.exe", "https://example.com"])`.

### Debug Mode
`python clap_launcher.py --debug`—shows amplitudes for tuning.

## 🐛 Troubleshooting

### Wake Word Not Detected
- **Mic Permissions**: Settings > Privacy & security > Microphone > Allow desktop apps.
- **Test Mic**: Run in terminal: Speak loudly—listen for feedback if looped.
- **Multiple Mics**: List devices:
```bash
python -c "import pyaudio; p = pyaudio.PyAudio(); [print(i, p.get_device_info_by_index(i)['name']) for i in range(p.get_device_count()) if p.get_device_info_by_index(i)['maxInputChannels'] > 0]"
```
Edit script if needed (add `input_device_index = 1` in `self.pa.open()`).
- **Quiet Room**: Speak clearly; try "jarvis" for testing.

### Claps Not Detected
- **Debug It**: `--debug` shows amplitudes—claps should spike >1800.
- **Clap Style**: Sharp, near mic; 0.7s apart for doubles/triples.
- **Noise**: Lower threshold if false negatives; raise if false positives.
- **Example Output**:
```bash
Amplitude: 2500 (threshold: 1800)  # Clap!
Amplitude: 1200 (threshold: 1800)  # Too quiet
```

### "No module named 'pvporcupine'"
```bash
pip install pvporcupine
```

### PyAudio/Dep Errors
- Reinstall: `pip install --upgrade pip wheel; pip install -r requirements.txt`.
- Python Version: Switch to 3.11/3.12 if 3.13 fails.

### Apps Not Launching
- **Paths**: Right-click shortcut > Properties > Target (use full .exe).
- **Not in PATH**: Always use full path with `subprocess.Popen`.
- **Store Apps**: `os.system("start whatsapp:")` or "notion:".
- **Test**: Open Command Prompt, type `start notepad`—if works, good.

### API Key Errors
- "Invalid key": No spaces/extra chars; regenerate at console.picovoice.ai.
- Device Limit: Free tier caps—new key if exceeded.

## 📝 Internal Workings
1. **OS Detection**: `platform.system()` picks "Windows".
2. **Wake Word**: Porcupine processes audio frames offline.
3. **Activation**: 5s window post-wake; claps counted by timing/amplitude.
4. **Launches**: `subprocess.Popen` for apps/URLs with 0.5s delays.
5. **Cleanup**: Graceful exit on Ctrl+C.

## 🔒 Privacy
- 100% local: Audio processed on-device; key only validates library.
- No transmission: Porcupine/PyAudio stay offline.

## 🤝 Contributing
Fork, tweak apps/wake words, improve clap detection algorithm or fix bugs—PRs welcome! Issues for suggestions.

## 📄 License
MIT License—use/modify freely.

## 🤝 Credits
Inspired by @TPAteeq's wake-up project—thanks for the foundation! https://github.com/TPAteeq/wake-up
---

**⭐ Star if useful!** Made with 💙 for Windows productivity. *Updated: February 12, 2026*












