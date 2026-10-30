# Recipes

**Outcome: four jobs Timeshift is
actually hired for, each as a short
sequence you can follow.** Each recipe
says which of its steps stand on this
guide's transcripts and which are
documented procedure — the division
that runs through the whole guide.

## A snapshot before a big upgrade

The canonical job. Before a distribution
upgrade, a kernel change, or a driver
swap:

```bash
sudo timeshift --create --comments "before the upgrade" --tags O
sudo timeshift --list
```

Do the upgrade only when the list shows
your snapshot. If the upgrade goes
wrong, chapter 8's restore — from the
running system, or from live media if
it no longer boots — is the way back.
The create-and-list pair is exactly the
pair the lab ran; the lab's list came
back empty for the device reasons of
chapter 3, and on a machine with a
device this is the recipe where the
tool earns its keep. *(Create and list:
attempted and transcribed, t03–t04.
The successful run and the restore:
documented.)*

## A snapshot before editing by hand

Smaller, and in practice more common.
You are about to edit system files
directly — network configuration, the
boot setup, a service's unit. One
on-demand snapshot first, named for the
job:

```bash
sudo timeshift --create --comments "before network changes" --tags O
```

If the edit goes well, the snapshot
costs a little space and can be deleted
in the tidy below. If it goes badly and
the damage is one file, chapter 9's
fetch-by-hand is the smaller rescue;
if it goes very badly, chapter 8.
*(Documented procedure on a verified
shelf.)*

## Tidying the shelf

On-demand snapshots accumulate — every
"before the…" leaves one. Look at the
shelf and delete by name the ones whose
moment has passed:

```bash
sudo timeshift --list
sudo timeshift --delete --snapshot '2026-10-12_09-14-55'
```

Deleting everything is `--delete-all`,
which asks before it acts unless `--yes`
rides along. The lab ran `--delete-all`
against its empty shelf (transcript
t05): Timeshift refused at the device
stage before reaching any question of
deleting, which is its own small lesson
— every operation, even housekeeping,
goes through the snapshot device, and a
device it cannot find stops them all
alike. Scheduled snapshots need no
tidying; chapter 7's counts prune them.
*(The refusal: transcribed. Deleting a
real snapshot: documented.)*

## Watching the shelf's appetite

A shelf that fills its partition turns
a safety tool into an incident. Two
numbers, checked once in a while:

```bash
sudo timeshift --list
sudo du -sh /path/to/timeshift/snapshots
```

The list's count against the `du` total
tells you the average cost of a snapshot
on your machine — mostly changes, per
chapter 6 — and whether your counts
from chapter 7 fit the partition you
gave them. The configuration even keeps
running totals of its own in
`snapshot_size` and `snapshot_count`,
written by real runs; the lab's stayed
at `"0"`, as an empty shelf's should.
*(Commands standard; the lab's shelf
was empty and its zeros are in
transcript t03's configuration.)*
