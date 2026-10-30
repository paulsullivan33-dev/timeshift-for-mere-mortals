# Timeshift for Mere Mortals

An update lands badly. A driver breaks,
a setting you changed on Tuesday is the
reason nothing works on Friday, and the
machine you need is sitting there being
broken at you. What you want is not a
backup of your documents — those are
fine. What you want is the *system* back
the way it was last week, before the
change. **Timeshift** is the program
that keeps that "before" for you. It
takes **snapshots**: complete, dated
copies of your operating system and its
settings, kept on a shelf. When a change
goes wrong, you pick a snapshot from
before it and Timeshift puts the whole
machine back.

A snapshot is cheap to keep because of
one trick: files that did not change
between two snapshots are stored only
once and shared between them, so a shelf
of ten snapshots costs little more than
one or two. And a snapshot is deliberately
*not* a backup. It usually lives on the
same machine it protects, so it does
nothing for you if the disk itself dies.
Chapter 1 draws that line early and
chapter 13 keeps it honest: Timeshift
undoes changes; it does not save you
from disasters. For disasters you want
a real backup as well — this series has
a guide for that way of thinking, and
chapter 12 points to it.

This guide explains what Timeshift
copies and what it leaves out, its two
very different snapshot engines, how to
take your first snapshot and prove it
exists, how the schedules and keeping
counts work, and how a restore puts a
machine back — full system, or one file
at a time by hand.

## What the lab could and could not do

Most guides in this series run their
tool end to end. This one has to tell
you something different, plainly, because
it shaped everything below.

The lab machine is a shared Ubuntu
24.04 sandbox. It runs as root, it has
rsync, and it happily ran the genuine
Timeshift **24.01.1** from Ubuntu's own
archive. What it does not have is any
usable disk: the kernel can see virtual
disks, but there are no block device
files in `/dev`, nothing carries a
filesystem or a UUID that any tool can
read, and the root filesystem is an
overlay rather than a disk partition.
Timeshift finds its snapshot device by
looking at exactly those things. So the
lab verified everything up to the
device — the install, the whole command
line, the configuration file, the
scheduler's cron entry, both modes'
behaviour — and then recorded, word for
word, the moment Timeshift refuses:

```text
Snapshot device not available
Device not found: UUID='d2c1af7e-21d2-4f2d-b35a-1929628383ee'
```

No snapshot was created in this lab, and
none is claimed. Every procedure past
that refusal — a completed snapshot, the
shelf, a restore — is taught from
Timeshift's own documentation and is
labelled documented-only wherever it
appears. `criteria.md` keeps the full
ledger: what ran, with the transcripts
in `timeshift-lab/` as evidence, and
what was only read.

That is less than this series usually
delivers, and it is also the most useful
thing this lab could show you. The
refusal above is the first thing that
goes wrong for real users on virtual
machines and containers, and chapter 14
turns it into a diagnosis.

## What this guide covers

- [**What Timeshift is**](what-timeshift-is.md) —
  the mental model: dated copies of the
  system itself, kept to undo changes,
  and the line between a snapshot and a
  backup
- [**The moving parts**](the-moving-parts.md) —
  the two programs, the configuration
  file, the snapshot device, the shelf a
  snapshot lives on, the hourly checker,
  and the logs — and which part holds what
- [**Installing Timeshift**](installing-timeshift.md) —
  the ordinary install, the version check
  that proves it runs, and the device list
  that proves it can see somewhere to work
- [**Two modes — rsync and Btrfs**](two-modes.md) —
  the two snapshot engines, what each one
  needs from your disk, and how to choose
  between them
- [**Your first snapshot**](your-first-snapshot.md) —
  one snapshot, taken on demand, with a
  comment and a tag — and the exact
  refusal you get when the device is not
  there
- [**Proving the snapshot**](proving-the-snapshot.md) —
  how to know a snapshot is real: the list,
  the shelf browsed by eye, and the
  hardlink arithmetic that makes ten
  snapshots cost like two
- [**Schedules and keeping counts**](schedules-and-retention.md) —
  boot, hourly, daily, weekly and monthly
  snapshots, how many of each to keep, and
  the hourly cron entry that drives it all
- [**Restoring**](restoring.md) — putting
  the whole system back from a snapshot,
  from the running machine or from live
  media when it will not boot
- [**Single files back by hand**](single-files-by-hand.md) —
  an rsync-mode snapshot is plain files,
  so one lost configuration file can be
  fetched back with ordinary tools
- [**What to leave out**](what-to-leave-out.md) —
  the exclude list, what Timeshift never
  copies anyway, and why your home
  directory is left alone on purpose
- [**Recipes**](recipes.md) — a snapshot
  before a big upgrade, a snapshot before
  editing by hand, tidying the shelf, and
  watching the disk space the shelf uses
- [**The neighbours**](the-neighbours.md) —
  Snapper, Kopia, rsync by hand and disk
  imaging: what each does that Timeshift
  doesn't, and vice versa
- [**Limits**](limits.md) — not a backup,
  the same disk is a single point of
  failure, Btrfs mode's strict layout,
  and the device Timeshift cannot do
  without
- [**Troubleshooting**](troubleshooting.md) —
  the device that is not found, the empty
  device table, the system disk that is
  not found, the first run's wall of
  warnings, and where the logs live
- [**Glossary**](glossary.md) — the terms,
  collected in one place

## What it doesn't cover

Backing up your documents, photos and
other personal files — Timeshift
deliberately leaves most of that alone,
and the Backups, Kopia and Restic guides
in this series cover it properly.
Encrypting snapshots or keeping them on
another machine — Timeshift does neither;
its shelf sits on a disk in the same
box. Rolling back a system whose disk
has physically failed — there is nothing
left to roll back *to* or *from*, and
that is the backup's job, again.

## How to use this guide

Read chapter 1 for the model and chapter
2 before installing anything — knowing
which part holds what turns later
confusion into a named problem. Chapter
4 decides the one setting everything
else depends on, your mode and your
snapshot device; chapter 3's device-list
check is the moment you find out whether
your machine can run Timeshift at all.
The lab's transcripts live in
`timeshift-lab/transcripts/`; the
configuration the lab wrote is
`timeshift-lab/timeshift.json`, beside
the template the package itself ships,
`timeshift-lab/default.json`.

---

## Part of the series

This guide is one of the [For Mere Mortals](https://paulsullivan33-dev.github.io/mere-mortals/) guides: plain-language guides to the tools programmers and makers use.

Related guides:

- [Snapper for Mere Mortals](https://paulsullivan33-dev.github.io/snapper-for-mere-mortals/)
- [Kopia for Mere Mortals](https://paulsullivan33-dev.github.io/kopia-for-mere-mortals/)
- [rsync for Mere Mortals](https://paulsullivan33-dev.github.io/rsync-for-mere-mortals/)
- [Backups for Mere Mortals](https://paulsullivan33-dev.github.io/backups-for-mere-mortals/)

## License

The writing in this guide is licensed under [CC BY 4.0](https://github.com/paulsullivan33-dev/timeshift-for-mere-mortals/blob/main/LICENSE). Code examples and lab scripts are licensed under the [MIT License](https://github.com/paulsullivan33-dev/timeshift-for-mere-mortals/blob/main/LICENSE-CODE).
