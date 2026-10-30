# What Timeshift Is

**Outcome: you can say what a Timeshift
snapshot holds, what it deliberately
leaves out, and why it is a way to undo
changes rather than a backup.** The
check is the three questions at the end
of this chapter — if you can answer
them, you have the model the rest of the
guide builds on.

Your computer's system — the operating
system, the programs, their settings —
is a large pile of files that changes a
little every day. Most changes are good.
Some are not: an update that breaks the
graphics, a configuration file edited
once too often, a program whose removal
took something else with it. The damage
in these stories is rarely to *your*
files. Your letter, your photos, your
code are usually sitting there untouched
while the machine around them misbehaves.

**Timeshift keeps dated copies of the
system pile, so a bad change can be
undone.** One run of Timeshift makes one
**snapshot**: a copy of the system as it
stands at that moment, stored under that
moment's name. A week of runs makes a
shelf of snapshots. Restoring one puts
every system file back the way the
snapshot recorded it — the bad update
un-happens, the edited file returns, the
removed program's pieces come home.
Timeshift was written by Tony George and
is now maintained with Linux Mint, which
ships it as standard equipment; it is
the tool Mint points its users at when
an update goes wrong.

## The trick that makes it affordable

A full copy of a system is gigabytes.
Keeping ten of them sounds like ten
times the gigabytes, and for the first
snapshot it nearly is. The saving is in
what happens next: **a file that has not
changed since the previous snapshot is
not copied again.** The new snapshot
points at the copy already on the shelf
— a **hardlink**, two names for one
piece of data. Only files that changed
take new space. A shelf of snapshots
therefore costs one full copy plus the
size of everything that changed between
runs, which on a settled system is
small. Chapter 6 does this arithmetic
properly, with the commands that prove
it on a real shelf.

## What it leaves out, on purpose

Timeshift is aimed at the system, not at
you. By default it does not fill its
snapshots with your home directory's
contents — your documents and pictures
are not what it is for, and chapter 10
explains the reasoning and the options.
It also never copies the parts of a
running system that are not really
files: the contents of `/dev`, `/proc`,
`/sys` and their kin are created fresh
by the kernel at every boot and are
excluded by design.

## A snapshot is not a backup

Say it plainly, because everything else
in this guide hangs on it. A backup
protects you when the *storage* fails:
the disk dies, the machine is stolen,
the house floods. Timeshift's shelf
normally lives on a disk inside the
same machine — often the very same disk
it snapshots. If that disk dies, the
system and its snapshots die together,
holding hands. What Timeshift protects
against is the far more common accident:
the change that should not have been
made, discovered hours or days later.
Undo, not rescue. You need both kinds
of protection, and they are different
tools; the Backups guide in this series
covers the rescue half.

## Two engines

Timeshift has two ways of making a
snapshot, called **modes**, and they
share almost nothing under the surface.
**RSYNC mode** copies files with the
rsync program onto any ordinary Linux
filesystem, using hardlinks for the
sharing trick. **BTRFS mode** asks the
Btrfs filesystem to snapshot its own
**subvolumes**, which is instant and
takes no copying at all, but demands
that your system was installed on Btrfs
in the layout Timeshift expects.
Chapter 4 is about choosing, and about
what each mode needs from your disk.

## The check

Three questions, answered from this
chapter alone:

1. Your graphics break after an update.
   Is that a Timeshift problem, a backup
   problem, or both?
2. Your disk dies completely. What does
   your shelf of snapshots do for you?
3. Why does keeping ten snapshots not
   cost ten times the disk space?

The answers: (1) a Timeshift problem —
restore last week's snapshot; (2)
nothing at all — that is what the
backup is for; (3) unchanged files are
shared between snapshots by hardlink,
so only changes take new space.
