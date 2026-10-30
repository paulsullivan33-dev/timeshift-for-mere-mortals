# The Neighbours

**Outcome: you can place Timeshift among
the tools that resemble it, and say in
one sentence what each is for instead.**
These tools are characterised from their
documentation and this series' other
guides, not benchmarked here; the
snapshot family is close enough that
the differences are worth stating
carefully.

## Snapper — the other snapshot manager

Snapper, SUSE's tool and the subject of
its own guide in this series, is
Timeshift's nearest relative: dated
snapshots of the system, kept to undo
changes. The differences are of
allegiance and automation. Snapper is
Btrfs-only by design — there is no
rsync-mode fallback — and it leans
further into the system's life, taking
snapshots automatically around package
operations, so every install or update
arrives with its own before-and-after
pair, and offering rollback from the
boot menu itself. Timeshift is the more
portable and the more deliberate tool:
two filesystems' worth of modes, a
window a newcomer can drive, and
snapshots that happen when the schedule
or the user says so. If your world is
Btrfs and package-driven, Snapper fits
it like a glove; if it is not, Timeshift
is the one that still works.

## rsync, by hand — the engine without the uniform

RSYNC mode is, mechanically, rsync with
`--link-dest` aimed at the previous
snapshot, plus everything Timeshift
wraps around it. You can build that
yourself — this series' rsync guide
does, step by step, and proves the
space arithmetic with real numbers. What
the hand-rolled version does not give
you is the rest of the uniform: the
schedule and its counts, the tags, the
device handling, the restore flow that
knows about bootloaders, and a list
command that reads the shelf. People
who need snapshots of *arbitrary
directories* — not the system — are
often better served by the hand-rolled
version or the next neighbour; system
rollback is the job Timeshift exists
for.

## Kopia, Restic — backups, properly so called

Kopia and Restic, both with guides in
this series, only sound like rivals.
They copy files into an encrypted,
deduplicated repository that can live
on another machine entirely, and they
are built to be the thing you restore
from when the disk is gone. That is the
rescue half of chapter 1's line, and
Timeshift does none of it — no
encryption, no remote storage, no
ambition beyond the machine it sits on.
The overlap is a shared trick
(deduplication there, hardlinks here)
and a shared word, "snapshot", meaning
a dated point to return to. A
well-kept machine runs one of these
*and* Timeshift, for different fears.

## Disk imaging — the whole disk, frozen

Clonezilla and plain `dd` copy a disk
or partition as one great slab, bit for
bit. An image is the most complete
snapshot imaginable and the least
convenient: making one is slow and
usually done offline, keeping several
is expensive, restoring one file from
one is a chore, and restoring at all
rewrites everything including the parts
that were fine. Imaging earns its place
at milestones — before a disk swap, for
a machine that must be cloned — while
Timeshift is for the ordinary Tuesdays
in between.

## The one-sentence version

Snapper if your system is Btrfs and
package-driven; rsync by hand if the
thing to snapshot is not the system;
Kopia or Restic for the backup Timeshift
is not; an image for the day everything
changes at once; and Timeshift for
rolling the system back when a change —
yours or an update's — goes wrong.
