# Two Modes — Rsync and Btrfs

**Outcome: you know which mode your
machine can use, and why.** The check:
`findmnt -no FSTYPE /` tells you what
your root filesystem is, and
`sudo timeshift --list-devices` tells
you whether Timeshift can see it. Read
the two answers against the rules below.

The mode is the first real decision in
Timeshift, and it is mostly made for
you by how your machine was installed.
The setting lives in the configuration
as `btrfs_mode` — `"false"` means rsync,
`"true"` means Btrfs — and the window's
settings page offers the same choice as
two radio buttons. The engines could
hardly differ more.

## RSYNC mode: copies files, works anywhere

In RSYNC mode a snapshot is a copy.
Timeshift runs rsync over the system,
leaving out the parts chapter 10 lists,
and writes the files into the new
snapshot's folder on the snapshot
device. The second snapshot runs rsync
again with the previous snapshot as a
reference: files that match are not
written — the new folder gets hardlinks
to the files already stored — and only
changed files take fresh space. Chapter
6 is devoted to proving that arithmetic.

What it needs: a snapshot device with
any ordinary Linux filesystem — ext4 is
the usual choice — and room on it for
roughly one full system copy plus
changes. The system being copied can
sit on any filesystem at all. The costs
are time (the first snapshot copies
everything, later ones copy the day's
changes) and honesty about space (the
sharing is real but the first copy is
whole). What it never needs is anything
special from the machine's layout,
which is why it is the mode that works
almost everywhere.

## BTRFS mode: no copying, strict demands

In BTRFS mode a snapshot is not a copy
at all. Btrfs, the copy-on-write
filesystem, can freeze a **subvolume** —
a named slice of the filesystem — into a
snapshot subvolume instantly, sharing
every block until something changes it.
Timeshift drives that: the system
subvolume (conventionally named `@`) is
snapshotted, and restoring swaps the
snapshot into `@`'s place. Making a
snapshot takes seconds and, at first, no
space at all.

The demands are strict, and they are
about how the machine was *installed*,
not how it is used now. The system must
live on Btrfs, in the standard layout
with `@` for the system and `@home` for
home directories that Ubuntu-style
installers create. A Btrfs system
installed some other way, and any
system on ext4 or another filesystem,
cannot use this mode at all. There are
further corners — a separate `/boot` on
another filesystem is not inside the
snapshot, for one — which the project's
documentation enumerates and chapter 13
summarises.

## The lab tried both

The lab machine fails each mode's entry
requirement in a different way, and the
two refusals are the clearest statement
of the rules this chapter teaches. In
RSYNC mode, with a configuration naming
a device UUID, the scripted create of
transcript t04 answered:

```text
Snapshot device not available
Device not found: UUID='d2c1af7e-21d2-4f2d-b35a-1929628383ee'
```

Discovery had found no devices at all,
so no UUID could resolve. In BTRFS mode
(transcript t05), the answer was shorter
and about the other side of the bargain:

```text
E: System disk not found!
```

The lab's root is an overlay, not a
Btrfs system disk in any layout, so
there was nothing for the mode to find.
On your machine, expect at most one of
these. A normal ext4 machine gets the
first only if its snapshot device is
missing or misnamed; a Btrfs machine in
the standard layout gets neither.

## Choosing

If your system is on Btrfs in the
standard `@`/`@home` layout, BTRFS mode
is the natural choice: snapshots are
nearly free, so you can keep many.
Otherwise, RSYNC mode — it asks nothing
of your system's layout, only for a
partition with room. Either way the
choice is visible in one place, the
configuration file, and either way the
shelf, the schedules and the restore in
the chapters ahead behave the same from
where you sit.
