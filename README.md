# 🔆 Keyboard Backlight Toggle on Fedora

This script toggles the keyboard backlight using `/sys/class/leds/input7::scrolllock/brightness` on Fedora 41 and similar distros.

---

## 🔧 Fedora Guide: Toggle Keyboard Backlight via Shortcut using `/sys/class/leds`

This guide is beginner-friendly and explains how to:

1. Detect which LED device controls your Scroll Lock or backlight.
2. Create a script to toggle the LED.
3. Set up a GNOME keyboard shortcut.
4. (Optional) Configure `sudo` so the script doesn't ask for a password.

---

### 🧪 Step 1: Identify the Correct LED Device

Open a terminal and run:

```bash
ls /sys/class/leds/
```

You’ll see something like:

```
input3::capslock  input3::numlock  input3::scrolllock
input7::capslock  input7::numlock  input7::scrolllock
phy0-led  mmc0::
```

We’ll test which `scrolllock` device is active.

**🔍 Check LED State:**

```bash
cat /sys/class/leds/input3::scrolllock/brightness
cat /sys/class/leds/input7::scrolllock/brightness
```

Now toggle each one manually and watch the LED or keyboard backlight:

```bash
# Turn ON each one manually
sudo sh -c "echo 1 > /sys/class/leds/input3::scrolllock/brightness"
sudo sh -c "echo 1 > /sys/class/leds/input7::scrolllock/brightness"
```

Then turn them OFF:

```bash
sudo sh -c "echo 0 > /sys/class/leds/input3::scrolllock/brightness"
sudo sh -c "echo 0 > /sys/class/leds/input7::scrolllock/brightness"
```

👉 The one that causes the light to turn ON/OFF is your **active LED**. Let’s say it's `input7::scrolllock`.

---

### ✏️ Step 2: Create the Toggle Script

Create a script that toggles the backlight:

```bash
sudo nano /usr/local/bin/toggle-kbd-backlight
```

Paste this:

```bash
#!/usr/bin/env bash
LED_PATH="/sys/class/leds/input7::scrolllock/brightness"
current=$(cat "$LED_PATH")

if [ "$current" -eq 1 ]; then
  echo 0 | sudo tee "$LED_PATH"
else
  echo 1 | sudo tee "$LED_PATH"
fi
```

Make it executable:

```bash
sudo chmod +x /usr/local/bin/toggle-kbd-backlight
```

---

### 🔐 Step 3: Allow Running Script Without Password (Optional)

To skip password prompt when running with `sudo`, run:

```bash
sudo nano /etc/sudoers.d/kbd-backlight
```

Add this line (replace `yourusername` with your actual user):

```
yourusername ALL=(ALL) NOPASSWD: /usr/local/bin/toggle-kbd-backlight
```

Save and exit.

---

### ⌨️ Step 4: Add a GNOME Keyboard Shortcut

1. Open **Settings** → **Keyboard**.
2. Scroll down to **Keyboard Shortcuts** → Click **Custom Shortcuts** → **Add Shortcut**.
3. Fill in:
   - **Name**: Toggle Backlight
   - **Command**: `sudo /usr/local/bin/toggle-kbd-backlight`
   - **Shortcut**: Press the `Scroll Lock` key or choose another combo (like `Ctrl + Alt + B`)

Now test your shortcut — it should toggle your keyboard backlight on and off!


