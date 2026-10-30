# Single Files Back by Hand

**Outcome: when exactly one file is
wrong — one configuration, edited badly
an hour ago — you can fetch its earlier
self out of a snapshot with ordinary
tools, without restoring anything.**
This chapter is documented-only in its
procedure: the lab had no shelf to reach
into. The layout it walks is Timeshift's
documented one, and the tools are the
ones every chapter so far has used.

## The idea

A full restore (chapter 8) is the
answer to a broken system. Most real
accidents are smaller: `/etc/fstab`
edited into nonsense, one service's
configuration improved into silence.
For those, RSYNC mode has a quiet
virtue — **a snapshot is not an archive
or an image; it is a directory tree of
ordinary files.** Nothing needs to be
"extracted" or mounted by special
software. You can walk into the shelf
with `ls` and walk out with the file.

## The walk

The snapshot device must be mounted.
Timeshift mounts it at `/run/timeshift`
while it works; outside those moments,
mount it wherever your system normally
mounts that partition, or by hand:

```bash
sudo mount /dev/sda2 /mnt
```

Then the path reads like a sentence.
The shelf is `/mnt/timeshift`; snapshots
sit in `snapshots/` under their dated
names; each holds a folder named for
the machine, and inside that folder the
system lies as it was — so yesterday's
`/etc/fstab` is at:

```text
/mnt/timeshift/snapshots/2026-10-29_09-00-01/localhost/etc/fstab
```

Fetch it the way you would fetch any
file, keeping the broken one until the
replacement has proved itself:

```bash
sudo cp /etc/fstab /etc/fstab.broken
sudo cp /mnt/timeshift/snapshots/2026-10-29_09-00-01/localhost/etc/fstab /etc/fstab
```

Then restart the affected service, or
reboot, and check. If the replacement
behaves, the incident is over and the
shelf never had to move. A `diff`
between the broken file and the
snapshot's copy before you copy is worth
the ten seconds it takes — it tells you
exactly which of your edits you are
undoing, and occasionally tells you the
file was not the problem after all.

## Two cautions

Permissions and ownership ride along
with `cp` only partly; for a system
file, prefer `sudo cp -a`, or rsync with
its archive flags, so the restored file
keeps the owner and mode the snapshot
recorded. And take the copy *from* the
snapshot — never edit files *inside* a
snapshot folder to "fix" them there.
In RSYNC mode those files are hardlinked
into neighbouring snapshots (chapter 6);
writing through one name can change
what another snapshot believes the past
looked like. The shelf is read-only in
spirit even where it is not in fact.

## In BTRFS mode

The same rescue exists, in the
filesystem's own shape: the snapshot is
a subvolume, it can be mounted read-only
like any other, and the file copied out
the same way. The path differs; the
principle — ordinary files, ordinary
tools, no restore required — is the
feature both modes share, and the one
you will use most often.
