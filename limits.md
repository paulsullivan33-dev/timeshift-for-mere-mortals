# Limits

**Outcome: you can name the walls
Timeshift runs into — including the one
this lab hit — before one of them names
itself during an incident.**

## It is not a backup

The limit everything else hangs from,
stated in chapter 1 and repeated here
because it is the one that hurts people:
the shelf lives on a disk in the same
machine, frequently the same disk as the
system. Disk death, theft, fire and
flood take the snapshots with the
system. Timeshift offers no encryption,
no copying to another machine, no cloud
— nothing off the box. It undoes
*changes*. Rescue is another tool's
job, and a machine with only Timeshift
on it is a machine whose files have no
protection from the failures that take
hardware with them.

## Your files are not in it

By default the snapshots hold the
system, not your home directory's
contents. A restore will not bring back
a deleted document, and — the mirror
image — a document created after the
snapshot survives a restore untouched.
Both halves surprise someone eventually.
Chapter 10 has the reasoning and the
options for including home; the limit
to carry away is that "the snapshot
from Tuesday" means Tuesday's *system*,
full stop.

## BTRFS mode's strict layout

BTRFS mode works only when the system
was installed on Btrfs in the standard
`@` and `@home` subvolume layout. Any
other layout, and every other
filesystem, is simply outside it. Pieces
of the machine that live outside the
system subvolume — a separate `/boot`
partition is the common one — are
outside the snapshot too, which can
leave a restored system and its boot
files politely disagreeing. RSYNC mode
has no such demands, which is the
trade: anywhere, at the price of
copying.

## The first snapshot is a whole copy

RSYNC mode's sharing starts with the
second snapshot. The first is a full
copy of the system and takes the time
and the space of one — gigabytes and
minutes, not the seconds of BTRFS mode.
A shelf also grows by every change
between snapshots, so a partition sized
with no headroom will fill, and a
Timeshift that cannot write is a
Timeshift that silently stops
protecting you until someone reads a
log.

## It needs a device it can see

The lab's wall, and the most fundamental
limit in this guide: Timeshift works
through real, discoverable block
devices with Linux filesystems and
UUIDs, found through the ordinary
device inventory. A machine without
them — this sandbox, with no block
device files in `/dev`, an overlay for
a root filesystem, and a `blkid` that
prints nothing — gives Timeshift
nothing to hold. Every operation,
create and list and delete alike,
refuses, and no configuration can
substitute: the lab's config was
complete and correct in form, and the
answer was still "Device not found".
Timeshift was written for machines with
disks, and inside sealed containers it
is a well-behaved program with nothing
to do.

## Two more, briefly

Restore needs something to restore
from *and* a way to run: a system too
broken to boot means the live-media
procedure of chapter 8, not a click.
And the console's first run is
conversational — faced with no chosen
device it asks, three times, into
whatever terminal it has; scripted work
must say `--scripted`, as chapter 5's
transcripts show, or be ready for the
question.
