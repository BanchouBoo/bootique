# bootique

Bootiqe is a theming system that uses a theme file to parse and output any number of template files.

## Features
- Any number of arbitrary key-value pairs for themes.
- Template files can be whatever you want, from an Xresources template to a shell script to a CSS file. Any plaintext file can be turned into a template.
- Templates can execute shell commands by implementing a special line.
- Dmenu support (rofi or other programs that take piped newline-delimited input can also be used by changing an evironment variable)

## Building themes and templates
Themes and templates are very simple to put together.

A theme file is done in a simple `key: value` format, with comment lines being lines that start with `!`, if you've ever made or modified an Xresources file it should feel familiar. Themes are expected to have the file extenstion `.theme`.

ex:
```
red: ff0000
name: Caleb
website: github.com
```

A template file is also very simple. For the most part the file should look exactly like a normal file with the exception of where you wanna insert theme values, which is done with `<keyname>`. Templates are expected to have the file extension `.template`.

ex:
```sh
#!/bin/sh
notify-send "My name is <name>!" # using the theme above, <name> will be replaced with Caleb
```

Hopefully from these simple examples you're starting to understand the versatility of this system and how easy it makes to put together a global theme to use with a wide array of different software and files.

But templates can be even more versatile by adding a special line to them to execute shell commands. The format to do so is `booexec: <shell stuff here>`. The `booexec` line can go anywhere but only the first instance of it will be read and executed. The `booexec` line will also be stripped from the output file and has a special `<file>` key to reference the path where the output file will go. So using the following example we can then run our script after it's parsed and we'll recieve the notification `My name is Caleb!` when we change our theme.

ex:
```sh
booexec: sh <file>
#!/bin/sh
notify-send "My name is <name>!"
```

For further details and a list of evironment variables you can set to customize things, you can read the output of `bootique --help` below.
```
Options:
    -h, --help
    	display this help page

    -t, --theme <theme path>
    	target theme file used to parse templates (not used with --menu)

    -p, --template <template path>
    	target template file or directory that the theme will be applied to, if not set then $BOOTIQUE_TEMPLATE_DIR will be used

    --menu
    	select the theme from a list using $BOOTIQUE_DMENU_COMMAND populated with files from $BOOTIQUE_THEME_DIR

Evironment variables:
    BOOTIQUE_THEME_DIR
    	directory where themes are stored, used for the --menu option
        DEFAULT = $XDG_CONFIG_HOME/bootique/themes

    BOOTIQUE_TEMPLATE_DIR
    	directory where templates are stored, used when no template file or directory is provided
        DEFAULT = $XDG_CONFIG_HOME/bootique/templates

    BOOTIQUE_OUTPUT_DIR
    	directory where theme files are output
        DEFAULT = $XDG_CACHE_HOME/bootique/theme

    BOOTIQUE_DMENU_COMMAND
    	command used for the --menu option
        DEFAULT = dmenu
```
