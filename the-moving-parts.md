# The Moving Parts

**Outcome: you can name each part of a
Timeshift setup and say which part holds
what.** The check: run
`ls /etc/timeshift /var/log/timeshift`
on a machine where Timeshift has run,
and match what you see to the parts
below. On the lab machine those two
listings are in the transcripts.

Timeshift looks like one program. It is
a handful of parts with clear jobs, and
most confusion about it is really
confusion about which part you are
looking at.

## The two front doors

There are two programs. `timeshift` is
the **console** tool: every operation —
check, create, list, restore, delete —
is a command with options, and it is
what this guide mostly drives, because
what it prints can be kept and checked.
`timeshift-gtk` is the **window**: the
same abilities behind buttons, plus the
first-run wizard and the settings
screens where most people choose their
mode and device. They read and write the
same configuration and the same shelf;
nothing done in one is hidden from the
other. The window needs a display — the
lab's headless machine answered its
launch with `Gtk-WARNING **: cannot open
display:` (transcript t05), which is
why this guide's evidence is all
console-shaped.

## The configuration file

Everything Timeshift believes about your
setup lives in one file:
`/etc/timeshift/timeshift.json`. Which
mode, which device, which schedules are
on, how many snapshots of each kind to
keep, what to exclude — all of it, as
plain JSON you can read. The package
ships the starting template at
`/etc/timeshift/default.json`; the lab's
copy of both files is kept beside this
guide in `timeshift-lab/`, and chapter 5
works through the fields that matter.
Timeshift runs as **root** — it copies
the whole system, so it could hardly do
otherwise — and you will drive it with
`sudo`.

## The snapshot device and the shelf

Snapshots live on a **snapshot device**:
one disk partition with a Linux
filesystem on it, named in the
configuration by its **UUID**, the long
identifier every filesystem carries.
When Timeshift works, it mounts that
device if it is not mounted already and
uses a directory named `timeshift` at
its top. Inside, `snapshots/` holds one
folder per snapshot, named by date and
time — `2026-10-30_09-00-01` and the
like — and inside each, a folder named
for the machine holds the copied system,
beside a small `info.json` describing
the snapshot: its date, its tags, your
comment. (That layout is described from
Timeshift's documentation. The lab never
got a device, so it never grew a shelf —
chapter 6 is honest about what that
means for proving things.)

One device is both the shelf and, often,
the system being copied. Timeshift is
content with that, and chapter 1 already
said why you should not be, entirely.

## The engines

Under the shelf sit the two engines of
chapter 4. In RSYNC mode the copying is
done by **rsync** itself — the package
depends on it (transcript t06 shows the
dependency list) — run with the options
that make each new snapshot share
unchanged files with the one before. In
BTRFS mode there is no copying at all:
Timeshift asks Btrfs to snapshot its
system subvolume, and the filesystem's
copy-on-write does the rest.

## The hourly checker

Nobody has to remember to run Timeshift.
When scheduled snapshots are configured,
Timeshift leaves a file in
`/etc/cron.d/timeshift-hourly` — the lab
machine has one, installed by the
`--check` run in transcript t03, and it
can be quoted in full because it is
four lines of substance:

```text
0 * * * * root timeshift --check --scripted
```

Once an hour, cron runs `timeshift
--check`. The checker compares the
schedule in the configuration with the
snapshots already on the shelf and makes
only the ones that are due — a daily
snapshot if the last daily is more than
a day old, and so on. `--create`, by
contrast, always makes one. That split
— cron wakes it hourly, the checker
decides — is the whole scheduling
machine, and chapter 7 returns to it.

## The logs and the working directory

Every run writes a log named for the
moment and the job —
`2026-10-30_08-55-59_ondemand.log` and
its siblings — into `/var/log/timeshift/`.
The logs are more talkative than the
screen: the lab's create attempt printed
two lines of refusal, while its log
showed the configuration being loaded,
the device being looked up, and the
exact point of failure (transcript t04).
When Timeshift surprises you, the newest
log is the first place to look; chapter
14 makes a habit of it. While it works,
Timeshift keeps a working directory at
`/run/timeshift`, where the snapshot
device is mounted.
