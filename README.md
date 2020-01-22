# focus-or-open-program

Small python script to focus or open program under X server.


## Introduction

I was annoyed with GNOME 3 inability to define a keyboard shortcut to attempt focus on a program window if any on current desktop of fallback to opening it.

I ended up with this small script that does the trick.


## General Usage

    $ focus-or-open-program --app-class <window-class> --app-launch-command '<launch-command>'

| argument               | description                              |
|------------------------|------------------------------------------|
| `--app-class`          | The program's window X window class      |
| `--app-launch-command` | The command to run to launch the program |


#### Finding out the window class of a program

To find out the X window class of a window, run _xprop_ in a terminal and then click on the window.

Search for the value of `WM_CLASS(STRING)`.

In should be in the form `"program", "Program"`.

You should then use the value `program.Program` as the argument for `focus-or-open-program`.


## Examples

#### Emacs

    $ focus-or-open-program --app-class emacs.Emacs --app-launch-command 'emacsclient -c -a ""'

To declare a shortcut in GNOME 3, go to `Settings` > `Keyboard Shortcuts`, all the way down and click on the `+` to add a custom shortcut.

![gnome_shortcut_settings](/gnome_shortcut_settings.png)


## Dependencies

 - python 3
 - wmctrl
 - xdotool


## Implementation details

_wmctrl_ is used to determine the list of windows for current desktop.

_xdotool_ is used to list the windows for a given class.

They share the same id system for windows (property `WM_TRANSIENT_FOR(WINDOW)` as returned by _xprop_).

I also explored using commands `xwininfo -root -children`, `xlsclients -l` but they seemed to be using another id  (property `WM_CLIENT_LEADER(WINDOW)` in _xprop_).

With `xprop -id <window_id>`, _window_id_ being either one of the 2 id system, we could join the results.

But this is cumbersome and not necessary for our use-case.


## References

 - http://tripie.sweb.cz/utils/wmctrl/
 - https://www.freedesktop.org/wiki/Software/wmctrl/


## Similar projects

There is a [similar script relying on the fluxbox wiki](http://fluxbox-wiki.org/Keyboard_shortcuts.html) (at the end of the page) that relies solely on _xdotool_ but I assume it does not distinguish between desktops.