# Troubleshooting

**Outcome: the failures this guide
actually met — and the ones a new
Timeshift user meets first — each with
the line that identifies it and the
move that answers it.** Every message
quoted here is one the lab's Timeshift
printed, in the transcripts.

## "Device not found: UUID='…'"

```text
Snapshot device not available
Device not found: UUID='d2c1af7e-21d2-4f2d-b35a-1929628383ee'
```

The configuration names a backup
device, and discovery cannot match that
UUID to any device on the machine. The
lab's case was total — no device
carried any UUID — but the ordinary
causes are smaller: the UUID was copied
wrong, the partition was reformatted
since (reformatting mints a new UUID),
or the disk is not attached. Compare
the configuration's `backup_device_uuid`
against `blkid`'s output, line by line.
If `blkid` prints nothing at all, your
problem is the next entry instead.

## The empty device table

```text
E: Error opening directory '/dev/mapper': No such file or directory

Devices with Linux file systems:

Num    Device  Size  Type  Label
------------------------------------------------------------------------------
```

`timeshift --list-devices` finds
nothing, and the line about `/dev/mapper`
says the inventory itself came up
empty. This is the signature of a
machine whose devices are not exposed
as ordinary block devices — a sealed
container or sandbox, like the lab's,
where `/dev` holds no disk nodes at all
and `lsblk -f` shows blank filesystem
and UUID columns. There is no
Timeshift-side fix: give the machine a
real, attached disk with a Linux
filesystem, visible to `blkid`, or run
Timeshift somewhere that has one. On
ordinary hardware and properly
configured virtual machines this table
has rows; if yours does not, believe it
and fix the machine, not the config.

## "System disk not found!"

```text
E: System disk not found!
```

BTRFS mode went looking for a system
installed on Btrfs in the standard
layout and found none — the lab's root
is an overlay, but the same answer
greets an ext4 system or a Btrfs system
laid out some other way. Either the
system genuinely is such an install
and something is hiding it, or BTRFS
mode is the wrong mode for this
machine: switch to RSYNC mode
(`btrfs_mode` to `"false"`, or pass
`--rsync`) and give it a snapshot
device instead.

## A wall of JSON warnings on the first run

```text
E: Failed to open file “/etc/timeshift/timeshift.json”: No such file or directory
** (process:72651): CRITICAL **: tee_jee_json_helper_json_get_bool: assertion 'jobj != NULL' failed
```

—and twenty-odd siblings (transcript
t02). This is Timeshift reading a
configuration file that does not exist
yet, one complaint per field it went
looking for. It is ugly and mostly
harmless; the program continues with
defaults. It stops the moment a real
configuration exists — run the window's
wizard once, or write the file from the
shipped `default.json` as chapter 5
does. Treat it as a missing-file
report, not a corruption report.

## "Failed to get input from user in 3 attempts"

The console wanted a decision — usually
"Select backup device" — and nobody
answered: three prompts, then "Aborted."
It happens when a command that expects a
terminal runs without one, in a script
or a pipe. Answer it in advance:
`--scripted` for the non-interactive
form (which then refuses cleanly, as
above, instead of asking), and
`--snapshot-device` or a correct
configuration to decide the question it
was asking.

## "No snapshots found"

`timeshift --list` read the shelf and
the shelf is empty — or the device it
read is not the device your snapshots
were written to. If you expected
snapshots, check which device is
selected before concluding they are
gone; a shelf on another partition is
invisible until that partition is the
chosen one. If the device is right and
the list is empty, the snapshots were
never made — chapter 7's cron file and
the newest logs say why.

## "cannot open display"

```text
(timeshift-gtk:73142): Gtk-WARNING **: cannot open display:
```

The window, on a machine with no
desktop — a server, a container, an SSH
session without forwarding. Nothing is
broken; use the console program, which
does everything the window does. This
guide is written that way throughout
for exactly this reason.

## When in doubt, read the newest log

Every run leaves a dated log in
`/var/log/timeshift/`. The screen shows
conclusions; the log shows the road to
them — the configuration being loaded,
the device being looked up, the cron
state being checked. The lab's
two-line refusal had a thirty-line log
behind it (transcript t04) that named
each step. Whatever Timeshift surprises
you with, start there.
