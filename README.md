# gloriousctl

A command‑line utility to adjust settings on **Glorious Model O/D** (and likely other SinoWealth‑made) mice on **Linux/BSD** :contentReference[oaicite:1]{index=1}.

## Overview

`gloriousctl` enables you to configure:
- DPI presets and selection
- Debounce time
- RGB lighting (effects, colors, brightness)
- Liftoff distance *(supported)*
- (Possibly more — depending on firmware)

This tool communicates directly with the mouse over HID and works without requiring official Glorious Core software.

## Man page

[libratbag](https://github.com/libratbag), which has a GUI
for configuration, has a driver based on further development of this code.

    Usage:
     gloriousctl --help
            Show this help text.
     gloriousctl --info
            Show the current configuration of the mouse.
     gloriousctl --listen
            Listen for and show DPI profile changes.
     gloriousctl [--set-...]
            Change persistent mouse settings.

    Available settings:
     --set-debounce-time 4-16
            Change click debounce time in milliseconds. Only use even numbers.
     --set-dpi DPI1,...
            Up to six DPIs can be configured.
     --set-dpi-color RRGGBB,...
            For each DPI the RGB color can be set.
     --set-effect effect-name
            Available RGB effects: off, glorious, breathing, wave, tail,
            single, breathing7, breathing1, rave
            single and breathing1 use one color, breathing7 seven, rave two.
     --set-colors RRGGBB,...
            Set the color(s) of the effect. Only effective with --set-effect.
     --set-brightness 0-4
            Set the brightness of the effect. Only effective with --set-effect.
     --set-speed 0-3
            Set the speed of the effect. Only effective with --set-effect.

    Supported mice:
     - Glorious Model D (VID 258a PID 0033)
     - Glorious Model O/O- (VID 258a PID 0036) (untested)

## Build requirements / Prerequisites

C compiler and libhidapi-hidraw. The libusb-based HIDAPI backend
should technically work, but it requires exclusive control over the
USB device, which is impractical for a mouse.

You’ll need:
- A C compiler (`gcc`, `make`)
- `git` (to clone the repo)
- HID API library with hidraw backend (`hidapi-devel` on Fedora)
- Optionally: root or udev permission access to the device

## Fedora 42 Installation Instructions

On Fedora systems, the following packages cover these requirements.

### 1. System update & dependencies

```bash
sudo dnf update
sudo dnf install gcc make git hidapi hidapi-devel
```

gcc and make build the tool.

hidapi is the runtime library; hidapi-devel supplies headers and the hidraw backend.

### 2. Clone and build the project

```bash
git clone https://github.com/enkore/gloriousctl.git
cd gloriousctl
make
```

This compiles gloriousctl binary.

Optionally install it system‑wide:

```bash
sudo make install
```

### 3. Add a udev rule to allow non-privileged access

Create /etc/udev/rules.d/99-glorious.rules:

```text
SUBSYSTEM=="hidraw", ATTRS{idVendor}=="258a", ATTRS{idProduct}=="0036", MODE="0666"
```

These identifiers (258a:0036) match a typical Model O wired mouse; adjust if your VID/PID differ.

Reload rules:

```bash
sudo udevadm control --reload-rules
```

Then unplug and replug the mouse.

## Basic Usage

Test connection:

```bash
./gloriousctl --info
```

You should see your mouse’s current settings.

Examples:

### Set debounce time to 6

```bash
sudo ./gloriousctl --set-debounce-time 6
```

### Configure DPI presets, breathing effect, colors & brightness

```bash
sudo ./gloriousctl \
  --set-dpi 400,800,1600 \
  --select-dpi 1 \
  --set-effect breathing \
  --set-colors FF0000 \
  --set-brightness 3
```

### To view all available options:

```bash
./gloriousctl --help
```

### To view the current config

```bash
./gloriousctl --info
Detected Glorious Model O/O-
Opening device /dev/hidraw7
131
Firmware version: V103
read cfg: 131 bytes
config:
  0000  04 11 00 00 00 00 00 00 64 06 04 34 f0 03 07 0f  ........d..4....
  0010  1f 00 00 00 00 00 00 00 00 00 00 00 00 ff ff 00  ................
  0020  00 00 ff ff 00 00 00 ff 00 ff 00 ff ff 46 00 00  .............F..
  0030  ff ff ff ff ff 00 01 00 40 ff 00 00 02 07 ff 00  ........@.......
  0040  00 00 ff 00 00 00 ff 00 ff ff ff ff 00 ff 00 ff  ................
  0050  ff ff ff 42 02 00 ff 00 00 00 ff 00 00 00 ff ff  ...B............
  0060  ff 00 00 ff ff ff ff ff fa 00 ff ff 00 00 ff 00  ................
  0070  00 ff 00 00 42 00 ff 00 fa 03 6a 02 42 02 ff 00  ....B.....j.B...
  0080  00 01 00                                         ...
XY DPI independent: no
[x] DPI setting 1: 400 DPI	#FFFF00
[x] DPI setting 2: 800 DPI	#0000FF
[x] DPI setting 3: 1600 DPI	#FF0000
[x] DPI setting 4: 3200 DPI	#00FF00
[ ] DPI setting 5: 100 DPI	#FF00FF
[ ] DPI setting 6: 100 DPI	#FF4600

RGB mode: Off
```

## Tips & Notes
* The tool uses hidraw backend by default. libusb alternative support exists but may conflict if device is mounted elsewhere.
* Settings such as “debounce” are saved to the device’s onboard memory, meaning they persist even when you switch computers.
* Some Linux tools (like Piper/libratbag) may not expose certain parameters (e.g. advanced debounce settings) that gloriousctl supports 
* Repository maintenance: though the tool is functional, activity has slowed — issues remain open and pull requests are infrequent. 

## Caveats

Possibly works with minor alterations with the wealth of sinowealth mice, since they all seem to use the exact same Windows utility to configure them (except a config file telling it which VID/PID to look for and what options exist). Since this is not the official OEM/ODM (sinowealth would be an interesting case study...) software, and this appears to modify the EEPROM/flash of the controller in your mouse, there is a chance that using this in some way could brick your mouse.

## Contributions & Support
For issues or suggestions, consult the GitHub repository issue tracker. Be aware that many issues remain unresolved and contributions are welcome 

