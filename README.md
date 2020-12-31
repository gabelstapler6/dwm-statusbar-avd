akuma-v-dwm
===========

akuma-v-dwm is a daemon that displays the status bar for window managers like
[dwm](https://dwm.suckless.org). It is modular, event driven, and efficient,
only recomputing the requested parts of the status bar during updates.

The Daemon
----------

The daemon creates a modular status bar by updating the X root window name
when it receives the USR1 signal. It takes an ordered list of modules as a
parameter, and calls a function in each module to compute a section of the
status bar. Each section is cached and only recomputed upon request. These
requests are made by creating empty files with the same names as the modules
in the `/tmp/avdd` directory, then sending the daemon a USR1 signal.

The Scheduler
-------------

The scheduler creates request files in `/tmp/avdd`, then sends the USR1
signal to the daemon. It can send one signal to update multiple status bar
sections by creating a request file for each section to update, and can send
the signal immediately, after some delay, or repeatedly at some interval.

Installation
------------

Clone the repo.

```Shell
git clone https://gitlab.com/narvin/avd
```

Usage
-----

The following examples can be executed manually or by putting them in, e.g.,
your `.xinitrc` file. Note the `&` after long-running commands to make them
run in the background. If the directory that you cloned the appliction into
is not in your path, be sure to include the path when calling `avdd` or `avds`.

Start the daemon to create a status bar with the default sections, prefix,
separators, and suffix.

```Shell
avdd &
```

Or, start the daemon to create a status with only the volume and date/time
sections, with the entire status between square brackets (prefix/suffix), and
each section surrounded by angle brackets (separators). Note that the first
left separator and the last right separator are stripped from the output, so if
you want them back, simply include them in the prefix and suffix as shown here.

```Shell
avdd 'vol-amixer dt' '[<' '<' '>' '>]' &
```

Next, schedule the battery info and the date and time to be updated at
the top of each minute.

```Shell
avds 'bat,dt' m true &
```

Then schedule the CPU and memory usage to be updated every 5 seconds.

```Shell
avds 'cpu mem' 5000 true &
```

Finally, you can add these commmands to your volume and brightness keybindings
to update the status when those keys are pressed. Note that these jobs don't
need to be backgrounded since they run immediately and exit.

```Shell
avds vol-amixer
avds bl
```

You can also bind keys to commands like those in the `cmd` directory to
perform actions and also update the status bar. For instance, you can bind
keys to these commands to control the backlight and volume while also updating
the status bar:

```Shell
./cmd/lowerbl
./cmd/raisebl
./cmd/lowervol-amixer
./cmd/raisevol-amixer
./cmd/mutevol-amixer
```

Modules
-------

To create your own module, write a bash shell script with a function called
`mod_<name>` where `<name>` is the filename of your script, with dashes
replaced with underscores. For instance, if your module file is called
`weather-wttr`, it should contain a function called `mod_weather_wttr`. This
function should print to stdout whatever you want to appear in its section
of the status bar (so it should be concise with no newline characters). Then
place your script in the `mod` directory.

To include your module output in the status bar, start the daemon with
the module list parameter and include the name of your script in the
list. For instance, if you wanted to have a status bar that consisited of
your hypothetical weather-wttr module followed by the date/time, you would
start the daemon like this: `avdd 'weather-wttr dt' &`.

Please feel free to submit a pull request to have your module
included as part of this repo. And for inspiration and examples of
modules you might want to create, check out the bar-functions from
[dwm-bar](https://github.com/joestandring/dwm-bar), which this project draws
heavily upon. Please note that those bar-functions are not compatible with
the akuma-v-dwm daemon, but they could very easily be modified to work with
this daemon.

Commands
--------

In the `cmd` directory are shell scripts that perform actions that effect
the status bar and update the status bar, such as changing the backlight or
volume levels. These commands can be bound to keys, so the action will be
carried out and the status bar will be updated when the key is pressed.

Please feel free to submit a pull request to have your command included as
part of this repo.

