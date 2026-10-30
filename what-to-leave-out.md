# What to Leave Out

**Outcome: your snapshots hold the
system and not the churn — you know what
Timeshift already excludes, what the
configuration's exclude list is for, and
why home directories stay out.** The
check: the `exclude` array in
`/etc/timeshift/timeshift.json`, empty
in the shipped template, is the list
this chapter teaches you to extend.

## What is never copied anyway

Some of the filesystem is not files in
any lasting sense. `/dev` and `/proc`
and `/sys` are the kernel's shop window,
rebuilt at every boot; `/run` is the
running system's scratch space; `/tmp`
is temporary by name and by nature.
Timeshift excludes these, and the other
usual mount points such as `/mnt` and
`/media`, by design and without being
asked — copying them would store
nothing real and restoring them would
make no sense. It also leaves out its
own shelf, for the obvious recursive
reason. This built-in list is part of
the program's documentation rather than
your configuration, and the lab's own
run notes it working: the create
attempt's log (transcript t04) shows
`add_default_exclude_entries()` running
before anything else was attempted.

## What it means to snapshot the system only

The larger exclusion is a decision, not
a mechanism: **by default, Timeshift's
snapshots are of the system, and the
contents of users' home directories are
not the cargo.** In RSYNC mode home
directories are left out unless you
deliberately include them; in BTRFS
mode the `@home` subvolume is a separate
choice, held in the configuration's
`include_btrfs_home`. The reasoning is
the shape of chapter 1's line. Home
directories are large, change hourly,
and are the backup's job — a backup
that versions them properly, encrypts
them, and keeps them somewhere else.
Folding them into Timeshift makes every
snapshot fat with change, fills the
shelf's partition with the very churn
the hardlink trick cannot smooth away,
and still leaves you without a real
backup. Keep the tools in their lanes.

## Your own exclude list

What remains is the tail of large,
pointless, or private system-side
material that only you can know about:
a scratch directory a build process
fills, a cache that can be regenerated,
a data directory a database rebuilds.
The configuration's `exclude` array
takes patterns for these, in the same
spirit as rsync's — a path, with `*` to
match within a name and `**` at the end
of a path to mean "this and everything
under it". The window's settings expose
the same list as a table of folders to
include or exclude. Add a pattern when
you can name the directory and say why
it is safe to lose; a restore will
simply not contain it, which is the
point, and also the risk — anything you
exclude is a thing a restore cannot
bring back.

Two patterns from real life show the
shape of the idea: the package cache
under `/var/cache/apt/archives`, which
is downloadable again, and a games or
media library parked under `/var` or
`/opt`, which is large, static, and
backed up by other means. Neither is in
the shipped template — the lab's
configuration, like the template it came
from, carries an empty list — because
both are judgements about *your*
machine, and Timeshift leaves them to
you.
