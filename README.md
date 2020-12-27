xrsb
====

xrsb (X Root Window Status Bar) can be used with window managers, such as
[dwm](https://dwm.suckless.org), that display a status bar based on the
name property of the X root window. The application consists of a daemon
(xrsbd) and a scheduler (xrsbs), and is meant to be efficient and precise,
only updating specific parts of the status bar when necessary.

The daemon maintains and updates the status bar (root window name). It traps
the SIGUSR1 signal that is used to tell it that it's time to update. A client,
such as the scheduler, can create action files in a designated action directory
to specify which parts of the status bar the daemon should update. When the
daemon receives the signal, it looks in the action directory for any actions
that is understands, updates the status bar, and removes those files.

The scheduler creates action files in the action directory, then sends the
SIGUSR1 signal to a daemon. The scheduler can send one signal to perform
multiple actions by creating multiple action files, and can create the action
file(s) and send the signal immediately, after some delay, or repeatedly at
some interval.

