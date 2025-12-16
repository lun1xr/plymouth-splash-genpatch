# plymouth-splash-genpatch
**Important Note: This repo is the source for the AUR package and is not meant to be run as-is**

A tool for generating plymouth bootsplash animations from video or gif files. Will patch Plymouth's two-step to run at the proper framerate.

For optimal output, use relatively small files as the entire content of the animation must be loaded into the initramfs. As a general rule, the longer and higher resolution the graphic is, the lower the framerate must be to maintain a relatively fast boot. I've found that around 25-27 seconds it'll cut and get overwritten by the sddm login (may vary by window manager).

In the future I may add a tool to compress/optimize the files automatically, but for now whatever you use as input will be replicated unmodified.

## Usage Instructions
The package consists of one command and a couple systemd units.

### Command
```shell
mksplash [-t title] [-i input] [-f fps] [-r w:h] [-u] [-v] [-h]
  -t TITLE    Set the title
  -i INPUT    Input file/path
  -f FPS      Framerate (defaults to 30; use alone to patch to desired framerate)
  -r W:H      Set resolution (maintains aspect ratio; covers by default) 
  -u          Unpatch two-step immediately (not for use with other flags)
  -v          Verbose
  -h          Show help

Notes:
- 'makepkg' must run as a non-root user. Invoke command via 'sudo' so builds run as SUDO_USER and only system changes use root.
Examples:

  # Patches two-step to 24 fps
  sudo mksplash -f 24

  # Creates splash, unpatches if necessary
  sudo mksplash -t "My Bootsplash" -i input.mp4

  # Creates splash at 1920:1080, patches to 60 fps
  sudo mksplash -t "My Bootsplash" -i input.mp4 -f 60 -r 1920:1080
```

### Systemd
Run the following to enable automatic patching on theme changes made through ```plymouth-set-default-theme``` by watching the plymouth config file.
```shell
sudo systemctl enable mksplash-theme-change.path --now
```
The service uses ```/usr/bin/wall``` to post notifications on patch, but does not block reboots, so though I don't believe there are any adverse effects aside from the patch not being applied, try not to reboot while it's happening.
