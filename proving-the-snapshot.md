# Proving the Snapshot

**Outcome: you can show, with commands,
that a snapshot exists, what it holds,
and what it costs.** This is the chapter
where this guide's lab has the least to
show and the most to teach: the lab's
proofs are all negative, and a negative
proof understood properly is what keeps
you from trusting a shelf that is not
there.

## The list does not lie

The first proof is always the list:

```bash
sudo timeshift --list
```

With a snapshot on the shelf, this
prints the device it read and a table of
snapshots: name, tag, and the comment
you attached. That table is the program
reading its own shelf and reporting —
the closest thing to ground truth the
tool offers. The lab's list, from
transcript t02, is the same command with
nothing behind it:

```text
Device : Not Selected
No snapshots found
```

"No snapshots found" is a complete
sentence. It does not mean "none that
matter" or "none today"; it means the
shelf this machine can see is empty,
and any plan that begins "restore the
snapshot from Tuesday" is a plan about
nothing. Run this command after every
change to your Timeshift setup — a new
device, a new schedule, a new machine —
and believe what it says.

## The shelf, browsed by eye

RSYNC-mode snapshots are plain files,
and the second proof is to look at them
with ordinary tools. On the snapshot
device, mounted where your system
mounts it, the shelf is a directory
named `timeshift`; inside `snapshots/`
sits one folder per snapshot named by
its moment, and inside that, a folder
named for the machine holding the
copied system — its `etc`, its `usr`,
its `var`, laid out as they were — plus
an `info.json` carrying the snapshot's
date, tags and comment. You can `ls`
your way to the very file you care
about; chapter 9 turns that into a
rescue. This layout is described from
Timeshift's documentation: the lab has
no shelf to photograph, and transcript
t05's check — looking for a `/timeshift`
directory and finding none — is the
honest local version of the same proof.

## The hardlink arithmetic

The third proof answers "what does the
shelf cost?" A snapshot folder's files
are shared with its neighbours by
hardlink, so the naive measurement
lies: `du` on each snapshot folder
reports each one's full logical size,
counting shared files again and again.
The true cost is the whole shelf
measured once:

```bash
sudo du -sh /path/to/timeshift/snapshots
```

together with the count of snapshots it
holds. Ten snapshots whose folders each
*claim* 20 GB, in a shelf that measures
26 GB altogether, are sharing exactly
as designed: one full copy, plus the
changes. The mechanics underneath —
rsync's `--link-dest`, comparing each
file with the previous snapshot and
linking instead of copying — are run
for real, with the numbers, in this
series' rsync guide; Timeshift is that
technique wearing a uniform, and the
arithmetic above is how you verify the
uniform fits. The commands in this
section are the standard ones for the
job; on the lab machine there was no
shelf to run them against, and the
guide does not pretend otherwise.

## A habit to keep

Prove the shelf the way you would prove
a backup: after setting up, after any
change, and once in a while for no
reason. `timeshift --list` for existence,
a browse for contents, `du` for cost.
Three commands, one minute — and you
will never be the person who discovers,
mid-incident, that the snapshots stopped
in March.
