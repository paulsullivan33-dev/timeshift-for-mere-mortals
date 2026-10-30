# Glossary

The terms this guide uses, collected in
one place. Each is defined where it
first appears too; this page is for the
second meeting.

**Backup device** — the partition whose
filesystem holds the shelf of snapshots,
named in the configuration by its UUID.
Often different from the partition the
system lives on; the two are the
configuration's backup and parent
devices.

**BTRFS mode** — the snapshot engine
that uses the Btrfs filesystem's own
subvolume snapshots. Instant and
copy-free, but only on a system
installed on Btrfs in the standard `@`
and `@home` layout.

**--check** — the console command cron
runs hourly. It compares the schedule
in the configuration with the shelf and
makes only the snapshots that are due.

**Count** — how many snapshots of one
scheduled kind to keep: `count_daily`
and its siblings in the configuration.
Beyond the count, the oldest of that
kind are deleted.

**Cron entry** — the file
`/etc/cron.d/timeshift-hourly`, which
Timeshift installs itself. It runs
`timeshift --check --scripted` once an
hour and is the whole of the scheduling
machine's heartbeat.

**Exclude** — a pattern in the
configuration naming paths a snapshot
leaves out. Some exclusions (`/dev`,
`/proc`, `/sys`, the shelf itself) are
built into the program; the list in the
configuration is yours to extend.

**Hardlink** — two names for one piece
of stored data. RSYNC mode's sharing
trick: a file unchanged since the
previous snapshot is not copied, the new
snapshot simply links to the stored
file, so only changes take new space.

**On-demand snapshot** — one made
because you asked, with `--create` or
the window's button, rather than by the
schedule. Tagged **O**, kept until you
delete it.

**Parent device** — the partition the
system being snapshotted lives on. See
**backup device**.

**Restore** — running a snapshot
backwards: replacing the system's files
with the snapshot's copies and rebooting
into the system as it then was. The
opposite journey from creating one, and
destructive to system changes made
since.

**RSYNC mode** — the snapshot engine
that copies the system with rsync onto
any ordinary Linux filesystem, sharing
unchanged files with the previous
snapshot by hardlink. Works almost
anywhere; the first snapshot is a full
copy.

**--scripted** — the console flag for
non-interactive runs. Questions the
program would ask a terminal are
instead answered by refusal: clean
errors, exit codes, no prompts.

**Shelf** — this guide's word for the
snapshot store: the `timeshift`
directory at the top of the backup
device, with one folder per snapshot
inside `snapshots/`.

**Snapshot** — a dated, complete copy
of the system — operating system,
programs and settings — as it stood at
one moment, kept so the machine can be
put back to that moment. Not, by
itself, a backup.

**Subvolume** — a named slice of a
Btrfs filesystem that can be
snapshotted and swapped independently.
The system conventionally lives in one
named `@`, home directories in `@home`.

**Tag** — the letter marking a
snapshot's kind: **O** on-demand, **B**
boot, **H** hourly, **D** daily, **W**
weekly, **M** monthly. Schedules keep
their counts per tag.

**UUID** — the long identifier a
filesystem carries, such as
`d2c1af7e-21d2-4f2d-b35a-1929628383ee`.
Timeshift names devices by UUID in its
configuration because device names can
change order; `blkid` prints the real
ones.
