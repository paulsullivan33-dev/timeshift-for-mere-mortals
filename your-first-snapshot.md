# Your First Snapshot

**Outcome: one snapshot, taken on
demand, with your own words attached to
it — or a refusal you can diagnose.**
The check is `sudo timeshift --list`:
your snapshot's row, or the "No
snapshots found" the lab got.

A first snapshot has three ingredients:
a chosen device, a mode, and the create
command itself. Most people meet the
first two in the window's first-run
wizard, which walks through mode and
device and writes the configuration.
This chapter does the same work at the
console, because the console's version
can be shown.

## The configuration the lab wrote

Timeshift keeps its beliefs in
`/etc/timeshift/timeshift.json`, and
the package ships the template at
`/etc/timeshift/default.json` —
twenty-two lines, every value a string,
booleans spelled `"true"` and `"false"`.
The lab's configuration (kept as
`timeshift-lab/timeshift.json`) started
from that template and changed four
things: the backup device UUID filled
in, the same UUID as parent device,
`do_first_run` set to `"false"` because
the configuration was being supplied by
hand, and the daily schedule switched
on. `btrfs_mode` stayed `"false"` —
RSYNC mode. Transcript t03 shows the
file in full.

Two fields deserve a word. The
**parent device** is where the system
lives; the **backup device** is where
the shelf lives. On most machines they
are different partitions, and on many
they are different disks. And the UUID
in the lab's file was freshly generated
and names nothing: the lab had to put a
UUID in the field because the field
must hold one, while knowing no device
on that machine carries any UUID at
all. On your machine you take the UUID
from real output — `blkid` prints it,
and `timeshift --list-devices` shows
the devices it belongs to.

## The create command

With a device chosen, a snapshot on
demand is one line:

```bash
sudo timeshift --create --comments "before the big upgrade" --tags O
```

`--comments` attaches your sentence to
the snapshot, where it will sit in every
listing — future you, mid-incident, will
be grateful for words instead of a bare
date. `--tags` marks the snapshot's kind
with a letter: **O** for on-demand, and
B, H, D, W, M for the scheduled boot,
hourly, daily, weekly and monthly kinds
of chapter 7. On-demand is the default.
While it runs, a successful create
reports the rsync work and finishes by
naming the snapshot it made; `timeshift
--list` then shows it. That successful
run is described from Timeshift's
documentation — the lab's run, below,
is the one this guide can show you.

## What the lab's run actually did

Transcript t04 is the whole story, and
it comes in two acts. Run plainly, with
no terminal attached, `--create` fell
into the first-run habit of asking which
device to use:

```text
Select backup device:

Num    Device  Size  Type  Label
------------------------------------------------------------------------------

Enter device name or number (a=Abort): E: Invalid input
```

An empty list, three prompts, then
"Failed to get input from user in 3
attempts" and "Aborted." The configured
UUID matched no discovered device, so
as far as Timeshift was concerned no
device had been selected at all.

Run with `--scripted` — the
non-interactive form the cron checker
itself uses — the same attempt refuses
cleanly, and this is the output to learn
to read:

```text
Snapshot device not available
Device not found: UUID='d2c1af7e-21d2-4f2d-b35a-1929628383ee'
```

Exit code 1. The run's log (in
`/var/log/timeshift/`, quoted in the
transcript) shows the machinery arriving
at that refusal honestly: the
configuration loaded, "Setting snapshot
device from config file", the lookup,
the failure. Nothing was copied, no
`timeshift` directory appeared on any
disk — transcript t05 confirms there is
still no `/timeshift` anywhere — and no
snapshot exists. On a machine whose
`--list-devices` shows a row and whose
configuration names that row's UUID,
this same command is the one that fills
the shelf. The difference is not in the
command. It is in whether the device
exists for Timeshift to find.
