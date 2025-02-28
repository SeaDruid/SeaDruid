> **This is a draft post.**
<!--
@todo Create setup scripts to automate all of this
-->

> This is my main generative literate programming environment where I mainly write, code, and play on Steam

- Desktop: i3-wm
- Login: Ly
- Browser: Brave
- Theme: Punk Runner fork

<br>
<br>
<hr>
<br>
<br>

# Archinstaller

```sh
# Zero drive; first get drive like "nvme0n1"
lsblk -o NAME,SIZE,MODEL
dd if=/dev/urandom of=/dev/nvme0n1 bs=4M status=progress

# Install arch instller keyring fix
pacman -Sy archlinux-keyring
archinstall
```

# Setup

```sh
sudo pacman -S iwd git less unzip
sudo pacman -S gnome-keyring
```

# Touchpad

```
sudo nano /etc/X11/xorg.conf.d/30-touchpad.conf

# Then add this
Section "InputClass"
    Identifier "touchpad"
    Driver "libinput"
    MatchIsTouchpad "on"
    Option "Tapping" "on"
    Option "TapButton2" "2"  # Two-finger tap for right-click
    Option "TapButton3" "3"  # Three-finger tap for middle-click
EndSection

```

# Install Brave Browser

```
sudo pacman -S --needed base-devel
sudo git clone <https://aur.archlinux.org/yay.git>
sudo chown -R  $USER:users yay
cd yay
makepkg -si
yay -S brave

```

**Notes:** When you run it for the first time it might ask you for a keyring if you installed gnome-keyring, which is needed for github copilot credentials. Brave normally has it's own encryption mechanism, but with gnome-keyring present it'll try using that instead.

# Fix resolution by lowering DPI

```
# ~/.Xresources
Xft.dpi: 220
xterm*faceName: DejaVu Sans Mono:size=10
xterm*background: #050f0e
xterm*foreground: #ffffff

```

The to merge the updates and refresh

```
xrdb -merge ~/.Xresources
# Then CTRL+WIN+R to refresh

```

# i3 theme

Add the following to `~/.config/i3/config`:

```
# Start i3bar to display a workspace bar (plus the system information i3status
# finds out, if available)
bar {
  status_command i3status

  colors {
      background #050f0e
      separator #FF1342
      focused_workspace #FF1342 #FF1342 #ffffff
      inactive_workspace #062621 #062621 #12FFBC
      urgent_workspace #ffc826 #ffc826 #050f0e
  }
}

# Window Colors
                        # border, bg, text, indicator, child_border
client.focused          #FF1342 #FF1342 #ffffff #FF1342 #FF1342
client.unfocused        #050f0e #050f0e #3D996B #FF1342 #050f0e
client.focused_inactive #333333 #5f676a #ffffff #484e50 #5f676a
client.urgent           #ffc826 #ffc826 #050f0e #ffc826 #ffc826
client.placeholder      #000000 #0c0c0c #ffffff #000000 #0c0c0c
```

then create an `~/.config/i3status/config` config:

```
mkdir -p ~/.config/i3status
cp /etc/i3status.conf ~/.config/i3/status/config

```

Then add this to it:

```
general {
        colors = true
        interval = 5
        color_good = "#12FFBC"
        color_degraded = "#ffc826"
        color_bad = "#FF1342"
        separator = " | "
}
```


## Theme inspiration
[](https://marketplace.visualstudio.com/items?itemName=TheEdgesofBen.punk-runner)


# Audio via HDMI

- First list your sinks `pactl list sinks`
- Find the HDMI and set it `pactl set-default-sink <sink-index>`

# VSCode

```
yay -S visual-studio-code-bin bun

# Fix sandbox issues
sudo chown root:root /opt/visual-studio-code/chrome-sandbox
sudo chmod 4755 /opt/visual-studio-code/chrome-sandbox

```

## First launch

Start with `code --password-store="gnome-libsecret"`

Then CTRL+SHIFT+P to open command palette, type "Configure runtime arguments", then add: `password-store":"gnome-libsecret`

## Copy + Pasting

If pasting doesn't work, remove keybindings

- press CTRL+K then CTRL+S
- search for "paste" and remove the 2 bindings (CTRL+V and CTRL+Insert)

# Steam

- Add `sudo pacman -S bubblewrap-suid` for sandboxing to work with keyring
- To allow playing of games like BG3, go to Settings > Compatability > Allow Steam Play

# Fonts and Emojis
- Install emojis `sudo pacman -S ttf-liberation noto-fonts noto-fonts-emoji ttf-dejavu noto-fonts-emoji`
- Edit `~/.config/fontconfig/fonts.conf`

```
<?xml version="1.0"?>
<!DOCTYPE fontconfig SYSTEM "fonts.dtd">
<fontconfig>
  <alias>
    <family>sans-serif</family>
    <prefer>
      <family>Noto Sans</family>
      <family>Noto Color Emoji</family>
    </prefer>
  </alias>
  <alias>
    <family>monospace</family>
    <prefer>
      <family>Noto Mono</family>
      <family>Noto Color Emoji</family>
    </prefer>
  </alias>
</fontconfig>
```

- Refresh fonts with `fc-cache -fv`
- Install emoji picker `yay -S x11-emoji-picker`
- Add keyboard shortcut to `~/.config/i3/config` with `bindsym $mod+period exec emoji-picker`

# Screenshot and screen recording

- Install:

```sh
# Screenshots
sudo pacman -S flameshot
```

- Add these keybindings to `~/.config/i3/config`

```sh
bindsym Print exec flameshot gui  # Full GUI interface on PrtScrn
bindsym Shift+Print exec flameshot gui -s  # Direct region selection
```
