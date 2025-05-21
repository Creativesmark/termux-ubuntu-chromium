# termux-ubuntu-chromium
Run Chromium GUI with extensions inside Ubuntu on Termux (Android)
# Chromium with Extensions on Android (Termux + Ubuntu + VNC GUI)

This guide will walk you through setting up Chromium with extension support inside an Ubuntu environment on Android using Termux, complete with GUI via VNC. Perfect for node farming and testing browser-based dApps.

--- remember to remove batrry optimisation for termux and the vnc/bvnc apk you will download 

## 1. Install Termux & Dependencies

```bash
pkg update && pkg upgrade -y
pkg install proot-distro wget git unzip -y
```

## 2. Install Ubuntu via proot-distro

```bash
proot-distro install ubuntu
proot-distro login ubuntu
```

## 3. Inside Ubuntu: Update and Install Basics

```bash
apt update && apt upgrade -y
apt install sudo curl wget git unzip nano -y
apt install software-properties-common -y
```

## 4. Install GUI (Xfce4) and VNC Server

```bash
apt install xfce4 xfce4-goodies tightvncserver dbus-x11 x11-xserver-utils -y
vncserver :1
```

### Stop VNC to configure resolution

```bash
vncserver -kill :1
nano ~/.vnc/xstartup
```

Replace contents with:

```bash
#!/bin/sh
xrdb $HOME/.Xresources
startxfce4 &
```

Then:

```bash
chmod +x ~/.vnc/xstartup
vncserver :1 -geometry 1280x720 -depth 24
```

## 5. Install Chromium with Extension Support

```bash
apt install chromium chromium-common -y
```

If not available via `apt`, download manually:

```bash
cd /tmp
wget http://ftp.us.debian.org/debian/pool/main/c/chromium/chromium_136.0.7103.113-1_arm64.deb
apt install ./chromium_136.0.7103.113-1_arm64.deb
```

### Install missing dependencies (if errors appear)

```bash
apt --fix-broken install
```

## 6. Launch Chromium with Safe Flags

```bash
echo -e '#!/bin/bash\nchromium --no-sandbox --disable-gpu --disable-software-rasterizer --password-store=basic --disable-features=UseOzonePlatform' > /usr/local/bin/chromeblast
chmod +x /usr/local/bin/chromeblast
```

Run it:

```bash
chromeblast
```

## 7. Access GUI on Android

### Recommended VNC Clients:

* [VNC Viewer (by RealVNC) on Google Play](https://play.google.com/store/apps/details?id=com.realvnc.viewer.android)
* [bVNC: Secure VNC Viewer on Google Play](https://play.google.com/store/apps/details?id=com.iiordanov.freebVNC)

### VNC Configuration:

* **Address:** `127.0.0.1`
* **Port:** `5901`
* **Password:** (set during `vncserver` setup)
* **Color Depth:** 24-bit
* **Resolution:** 1280x720 or 1024x600 (adjust to screen)
* **Input Mode:** Touchpad (recommended for bVNC)

## 8. Manage Extensions

### Chromium Extension Directory

```bash
cd ~/.config/chromium/Default/Extensions
```

To remove an extension:

```bash
rm -rf <extension_id>
```

## 9. Optional: Clean Extension Profile

```bash
mkdir -p ~/clean-profile/Default/Extensions/
apt install rsync -y
rsync -av --exclude='<bad_extension_id>' ~/.config/chromium/Default/Extensions/ ~/clean-profile/Default/Extensions/
```

Then launch Chromium with the clean profile:

```bash
chromeblast --user-data-dir=/root/clean-profile
```

---

## Notes

* Extensions may not show up if they're corrupted or unsupported.
* Chromium might crash if a bad extension is installed—use clean profiles when testing.
* Always run Chromium with `--no-sandbox` on root environments.

## Credits

Tested and built in a Termux Ubuntu environment using proot + Chromium + xfce4 + VNC.

Feel free to fork, contribute, or report issues.

---

**GitHub:** \[Creativesmark]
**Twitter/X:** \[@i2playy]
**Built for node runners, tinkerers, and hackers.**
