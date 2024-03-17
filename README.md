# midisndiokontrol
by Morgan Aldridge <morgant@makkintosshu.com>

## OVERVIEW

A WIP (work in progress) utility that lets you control [sndio](https://sndio.org/) devices & state, plus X11 media applications, under [OpenBSD](https://www.openbsd.org/) using a MIDI control surface. It's currently being developed & tested with a Korg nanoKONTROL2 (hence 'kontrol' in the name), but I plan to make it much more generic and configurable. It is currently just a wrapper around OpenBSD's [midicat(1)](http://man.openbsd.org/midicat), [sndioctl(1)](http://man.openbsd.org/sndioctl), [xdotool(1)](https://github.com/jordansissel/xdotool), but I expect to rewrite it in C once I have stabilized the concept more, for performance reasons.

*IMPORTANT:* _I am not a regular user of MIDI devices, nor have I delved into the spec yet, so I'm likely using incorrect terminology at this point. Please feel free to suggest corrections/improvements."_

## FEATURES

### CURRENT

`midisndiokontrol` can currently handle MIDI key events from the Korg nanoKONTROL2 and trigger:
    * X11 key, keyup, or keydown events for various standard `XF86Audio*` media keys (via `xdotool`)
    * Set, unset, or toggle mute for `sndio` devices (via `sndioctl)
    * Set a knob/slider value as the level for a `sndio` input, output, or application device (via `sndioctl`)

The script is currently relatively straightforward to modify for your specific MIDI input device, `sndio` devices, and X11 applications. I suggest:

1. Run `midicat -d -q midi/<device_num>`
2. Press a key on your MIDI input device
3. Look at the 2nd column of hex output in the output of Step 1 to determine the key hex code
4. Edit the appropriate `key_\*` variable content and set it to the hex code you identified
5. Update the `"$key_")` handler in the `case` statement for the appropriate key to perform the action you'd like
6. Go to Step 2 and repeat until you've updated as you see fit

### PLANNED

* Make more configurable, especially the following for any MIDI key input:
    * MIDI key hex ID
    * Event type (i.e. "key", "down", "up", "value")
    * Action type (i.e. `xdotool`, `sndioctl`, etc.)
    * Action parameters (e.g. `key <name>`, `keydown <name>`, `keyup <name>` for `xdotool` or `<device>.level=<value>` or `<device>.mute=<value>` for `sndioctl`)
* Create defaults for various devices
* Monitor `sndioctl -m` output for device value state changes and send MIDI commands to control surface to illuminate buttons, as appropriate (e.g. on Korg nanoKONTROL2: change 'play' button to green when playing and orange when paused, change fast-forward/rewind buttons to orange/green while depressed, change 'mute' buttons for individual input/output devices to red when they are muted, etc.)
* Rewrite in C for increased performance

## PREREQUISITES

* [xdotool](https://github.com/jordansissel/xdotool)

## USAGE

1. Install `xdotool`: 
    `doas pkg_add xdotool`
2. Connect your MIDI device
3. Either use midicat(1) to send your MIDI device's input to `midithru/0` as follows, or update the `midisndiokontrol` script to specify the specific MIDI device to use: 
    `midicat -q midi/0 -q midithru/0 &`
4. Execute `midisndiokontrol` to start it processing MIDI events and triggering `sndioctl` changes or `xdotool` keyboard input events

## SIMILAR UTILITIES

You might want to check out these similar projects for controlling OpenBSD's sndiod(8) from X11:

* [sndiokeys](https://github.com/ratchov/sndiokeys)
* [xsndiomenu](https://github.com/morgant/xsndiomenu)

## LICENSE

Released under the [MIT license](LICENSE).
