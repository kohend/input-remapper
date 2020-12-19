<h1 align="center">Key Mapper</h1>

<p align="center">A tool to change and program the mapping of your input device buttons.</p>

<p align="center"><img src="readme/pylint.svg"/> <img src="readme/coverage.svg"/></p>

<p align="center"><img src="readme/screenshot.png"/></p>
<br/>

## Usage

To open the UI to modify the mappings, look into your applications menu
and search for 'Key Mapper' in settings. You can also start it via 
`key-mapper-gtk`. It works with both Wayland and X11.

If stuff doesn't work, check the output of `key-mapper-gtk -d` and feel free
to open up an issue here.

##### Macros

It is possible to write timed macros into the center column:
- `k(1).k(2)` 1, 2
- `r(3, k(a).w(500))` a, a, a with 500ms pause
- `m(Control_L, k(a).k(x))` CTRL + a, CTRL + x
- `k(1).h(k(2)).k(3)` writes 1 2 2 ... 2 2 3 while the key is pressed

Documentation:
- `r` repeats the execution of the second parameter
- `w` waits in milliseconds
- `k` writes a single keystroke
- `m` holds a modifier while executing the second parameter
- `h` executes the parameter as long as the key is pressed down
- `.` executes two actions behind each other

Syntax errors are shown in the UI on save. each `k` function adds a short
delay of 10ms between key-down, key-up and ad the end that can be configured
in `~/.config/key-mapper/config`.

Bear in mind that anti-cheat software might detect macros in games.

##### Key Names

Check the autocompletion of the GUI for possible values. You can also
obtain a complete list of possiblities using `key-mapper-service --key-names`.
Examples:

- Alphanumeric `a` to `z` and `0` to `9`
- Modifiers `Alt_L` `Control_L` `Control_R` `Shift_L` `Shift_R`
- Mouse buttons `BTN_LEFT` `BTN_RIGHT` `BTN_MIDDLE` `BTN_SIDE` ...
- Multimedia keys `KEY_NEXTSONG` `KEY_PLAYPAUSE` ...

##### Gamepads

Joystick movements will be translated to mouse movements, while the second
joystick acts as a mouse wheel. All buttons, triggers and D-Pads can be
mapped to keycodes and macros. Configuring the purpose of your joysticks
is currently done in the global configuration at `~/.config/key-mapper/config`.

The D-Pad can be mapped to W, A, S, D for example, to run around in games,
while the joystick turns the view.

Tested with the XBOX 360 Gamepad. On Ubuntu, gamepads worked better in
Wayland than with X11 for me.

## Installation

The tool shows and logs if there are issues, but usually, independent of the
method, you should add yourself to the `input` and `plugdev` groups so that
you can read information from your devices. You have to start the application
via sudo otherwise. You may also need to grant yourself write access to
`/dev/uinput` to be able to inject your programmed mapping.

There is a shortcut for configuring this stuff:

```bash
sudo key-mapper-service --setup-permissions
# now log out and back in
```

##### Manjaro/Arch

```bash
pacaur -S key-mapper-git
```

##### Ubuntu/Debian

```bash
wget "https://github.com/sezanzeb/key-mapper/releases/"\
"download/0.3.1/python3-key-mapper_0.3.1-1_all.deb"
sudo dpkg -i python3-key-mapper_0.3.1-1_all.deb
```

##### Git/pip

Depending on your distro, maybe you need to use `--force` to get all your
files properly in place and overwrite a previous installation of key-mapper.

```bash
# method 1
sudo pip install git+https://github.com/sezanzeb/key-mapper.git
# method 2
git clone https://github.com/sezanzeb/key-mapper.git
cd key-mapper && sudo python3 setup.py install
```

## Roadmap

- [x] show a dropdown to select valid devices
- [x] creating presets per device
- [x] renaming presets
- [x] show a mapping table
- [x] make that list extend itself automatically
- [x] read keycodes with evdev
- [x] inject the mapping
- [x] keep the system defaults for unmapped buttons
- [x] button to stop mapping and using system defaults
- [x] highlight changes and alert before discarding unsaved changes
- [x] automatically load presets on login for plugged in devices
- [x] make sure it works on wayland
- [x] support timed macros, maybe using some sort of syntax
- [x] add to the AUR, provide .deb file
- [x] basic support for gamepads as keyboard and mouse combi
- [x] executing a macro forever while holding down the key using `h`
- [x] mapping D-Pad directions as buttons
- [ ] support for non-GUI TTY environments
- [ ] mapping joystick directions as buttons
- [ ] configure joystick purpose via the GUI and store it in the preset
- [ ] automatically load presets when devices get plugged in after login (udev)
- [ ] mapping a combined button press to a key
- [ ] start the daemon in the input group to not require usermod somehow
- [ ] configure locale for preset to provide a different set of possible keys

## Tests

```bash
pylint keymapper --extension-pkg-whitelist=evdev
sudo pip install . && coverage run tests/test.py
coverage combine && coverage report -m
```

To read events, `evtest` is very helpful. Add `-d` to `key-mapper-gtk`
to get debug output.
