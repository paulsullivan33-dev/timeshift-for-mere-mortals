# Restoring

**Outcome: you know how a restore works,
what it replaces, and how to reach your
snapshots when the machine will not
boot.** Read this plainly first: **this
chapter is documented-only.** The lab
never made a snapshot, so it never
restored one, and no restore was
attempted against the lab machine's own
system — that would have been reckless
even if one had existed. Everything
here is from Timeshift's documentation,
and it is labelled so you can weigh it
differently from the chapters with
transcripts behind them.

## What a restore is

A restore runs a snapshot backwards.
You choose a snapshot; Timeshift works
out which device the system should be
restored onto — ordinarily the one it
came from — and then replaces the
system's files with the snapshot's
copies. In RSYNC mode that is a large,
careful rsync in the opposite direction;
in BTRFS mode it is the subvolume swap
the snapshot was always aimed at. When
it finishes, the machine reboots into
the system as the snapshot recorded it:
the bad update gone, the broken setting
back, the removed pieces restored.

Understand what that sentence covers.
The *system* returns. Files in your home
directory were never in the snapshot by
default, so they are not restored and —
the good half of the same news — not
overwritten either: your documents sit
through a restore untouched. What you
did to the *system* after the snapshot,
good and bad alike, is undone. A restore
is a one-way door for system state,
which is why the comment you wrote in
chapter 5 matters: pick the snapshot
from *before* the change you regret,
not merely the newest one on the shelf.

## Restoring from the running machine

If the machine still boots and runs,
this is the ordinary path. In the
window: select the snapshot, press
Restore, confirm the target device,
let it work, reboot. At the console the
same flow is:

```bash
sudo timeshift --restore
```

which asks, step by step, which
snapshot and which target; or, with the
choices made up front,

```bash
sudo timeshift --restore --snapshot '2026-10-30_09-00-01' --target /dev/sda2
```

using the snapshot's name exactly as
`timeshift --list` prints it. One
decision in the flow deserves forethought:
the **bootloader**. Restore offers to
reinstall GRUB so the restored system
boots cleanly — `--grub-device` names
where, and `--skip-grub` declines. On an
unchanged machine the defaults are
usually right; on a machine whose disks
have been rearranged since the snapshot,
think before accepting them.

## Restoring when it will not boot

When the system is too broken to run
Timeshift, the snapshots are still
reachable, because they are just a
directory on a disk. Boot the machine
from live media — a Linux Mint or
Ubuntu USB stick — install Timeshift in
that live session if it is not already
there, and run it. It finds the shelf on
the internal disk, and the restore
proceeds onto that disk as its target.
This is the procedure the project
documents for exactly this emergency,
and it is the reason the shelf's
plainness matters: no special format,
no key, nothing between the live system
and your files but a mount. It is also
worth rehearsing mentally before you
need it — the middle of an incident is
a bad classroom.

## After the restore

Reboot, log in, and check the thing that
was broken. Then, while the lesson is
fresh, take a fresh on-demand snapshot
of the now-healthy system with a comment
saying so. The shelf has done its job;
start it again from a known-good place.
