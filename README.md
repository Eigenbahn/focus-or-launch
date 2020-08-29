# focus-or-launch

Small script to focus or launch program under X server.


## Introduction

I was annoyed with most DE / WM inability to define a keyboard shortcut doing the following:

 - attempt focus on a window for program, on current desktop, if any
 - launch the program as a fallback

I ended up with this small script that does the trick.

It can act as a nice complement to [xbindkeys](https://www.nongnu.org/xbindkeys/) & [wmutils](https://github.com/wmutils) (albeit less minimal) for multi-desktop setups.

For more context, read the [accompanying blog post](https://www.eigenbahn.com/2020/01/22/gnome-shortcut-focus-or-launch).


## General Usage

    $ focus-or-launch --app-class <window-class> --app-launch-command '<launch-command>'

| argument               | description                              |
|------------------------|------------------------------------------|
| `--app-class`          | The program's window X window class      |
| `--app-launch-command` | The command to run to launch the program |


#### Finding out the window class of a program

To find out the X window class of a window, run _xprop_ in a terminal and then click on the window.

Search for the value of `WM_CLASS(STRING)`.

In should be in the form `"program", "Program"`.

You should then use the value `program.Program` as the argument for `focus-or-launch`.


## Examples

    All those examples take the following command, launching an Emacs client frame.

#### Manual execution

    $ focus-or-launch --app-class emacs.Emacs --app-launch-command 'emacsclient -c'


#### xbindkey

Put this entry in your `~/.xbindkeysrc`:

    "focus-or-launch --app-class emacs.Emacs --app-launch-command 'emacsclient -c'"
        Mod4 + e


#### Gnome3

Go to `Settings` > `Keyboard Shortcuts`, all the way down and click on the `+` to add a custom shortcut.

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

There is a [a script on the fluxbox wiki](http://fluxbox-wiki.org/Keyboard_shortcuts.html) (at the end of the page) that relies solely on _xdotool_ but I assume it does not distinguish between desktops.

StackOverflow user [desgua](https://askubuntu.com/users/12943/desgua) shared [another implementation](https://askubuntu.com/questions/127284/command-to-launch-an-application-or-to-show-its-window-if-already-launched) that likewise doesn't distinguish between desktops.
