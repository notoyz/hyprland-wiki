After you have installed Hyprland, you can either launch it from a TTY with
`Hyprland` or from a login manager. Although login managers aren't officially
supported, you might want to use `SDDM`, as it's been working flawlessly with wayland
compositors.

It is recommended you have `kitty` installed for terminal access, (example and
autogenerated configs have it bound to <kbd>SUPER</kbd> + <kbd>Q</kbd>).
Alternatively, manually change it in the config before launching Hyprland.

If you have an Nvidia card, please also take a look at
[The Nvidia page](../../Nvidia) before launching. You should **first** make a
wrapper, as described in the section below, then follow the instructions from
the Nvidia page, and then continue on with sections below.

{{< hint type=important >}}
Running Hyprland in a VM is **_unsupported_**. VMs employ a bunch of tactics that
mess with Hyprland, on top of the fact that they do _not_ provide a GPU.

If running Hyprland via `WLR_RENDERER_ALLOW_SOFTWARE=1 Hyprland` does not work,
you are out of luck. Try installing it on a real machine.

Although it's 100% possible to use Hyprland in a VM, if it's not launching it's 99.9%
of the times your fault. Please also keep in mind software rendering is _REALLY_ slow.
{{</ hint >}}

# Wrapping the launcher (recommended)

With Xorg, you get the `.xinitrc`. With Hyprland, you can create your own...
kind of.

Make an executable file somewhere in your `PATH`, for example `~/.local/bin/`,
called (for example) `wrappedhl`

In it, put:

```bash
#!/bin/sh

cd ~

export _JAVA_AWT_WM_NONREPARENTING=1
export XCURSOR_SIZE=24

exec Hyprland
```

You can add as many exported envvars as you need (Nvidia users might need a
lot), but it's recommended to have _at least_ the shown two.

You should now launch Hyprland with `wrappedhl` instead of `Hyprland`. Make sure
to copy your `.desktop` file in `/usr/share/wayland-sessions/` and edit it if you use a
login manager! You might need to put the full path in it, as login managers are
usually not ran through the user account.

{{< hint type=important >}}
It is highly recommended to copy the desktop file to e.g. `wrapped_hl.desktop` instead of editing
the provided one, as many package managers (and `sudo make install`) will **overwrite**
the desktop file on updates. If you manually build Hyprland, consider using `sudo make cleaninstall` to preserve `hyprland.deskop`
{{< /hint >}}

# Immediate

**OMG MY SCREEN IS BROKEN, FLASHY TEARY!** -> see the bottom of this page

Once you log in, you'll be greeted with a yellow warning that will give you some
basic keybind info of your pre-generated config.

To make the warning to go away, remove the `autogenerated=1` line from hyprland.conf

# Monitors

Use `hyprctl monitors` to list available outputs. `hyprctl` will _not_ tell you
what your monitor is capable of though, so if you want to check your resolution / refresh rate,
use a tool like `wlr-randr`.

Then, you can configure your outputs with

```bash
hyprctl keyword monitor NAME,RES@HZ,OFFSET,SCALE
```

`NAME` is the name of the display, e.g. `DP-1`. Can be empty for a global rule.

`RES@HZ` is the resolution and refresh rate, e.g. `1920x1080@144`. Can be
`preferred` for auto-detection.

`OFFSET` is the position of the monitor, e.g. `0x0`. Can be `auto` to
automatically add it to the right of the viewport.

`SCALE` is the display scale, e.g. `1`

example command:

```plain
hyprctl keyword monitor DP-3,1920x1080@240,1920x0,1
```

{{< hint type=important >}} These changes are **not** permanent! If you want to
make those changes persist, configure your outputs in the config! {{< /hint >}}

# Proper configuring

Head onto the
[Configuring Hyprland page](../../Configuring/Configuring-Hyprland) to learn all
about configuring Hyprland to your likings.

# Critical software

See the [Must-have Software page](../../Useful-Utilities/Must-have) for the crucial
things to make Wayland / Hyprland / other apps work correctly.

## Apps / X11 replacements

See the [Useful Utilities page](../../Useful-Utilities) and the
[Sway wiki page](https://github.com/swaywm/sway/wiki/Useful-add-ons-for-sway)
just about that.

## Screenshare / GTK slow launch issues

[FAQ (GTK issue)](../../FAQ/#some-of-my-apps-take-a-really-long-time-to-open)

[FAQ (screensharing)](../../FAQ/#screenshare--obs-no-worky)

## Screen broken on launch

This usually happens due to your monitor not being very happy about the default
settings.

You can get your monitor's name(s) from the TTY.

Exit hyprland, and then:

- `cat /tmp/hypr/$(ls -t /tmp/hypr | head -n 1)/hyprland.log | grep monitor`
  will give you a bunch of logs about the connected monitors. Names like `DP-x`
  or `HDMI-x` etc are your monitor names.
- edit `~/.config/hypr/hyprland.conf`
- replace the `monitor=` line with `monitor=NAME,RES@Hz,OFFSET,SCALE`, for
  example `monitor=DP-1,1920x1080@60,0x0,1` (See [Monitors](#monitors) for more
  info about the values). You can also add multiple of those for multi-monitor
  setups.

After this, upon launching Hyprland again, everything should be fine, provided
you set an appropriate mode for your monitors.
