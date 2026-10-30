# Schedules and Keeping Counts

**Outcome: Timeshift makes the snapshots
you chose, at the rhythm you chose, and
keeps the number you chose — no more.**
The check: `cat /etc/cron.d/timeshift-hourly`
shows the heartbeat, and the schedule
fields in `/etc/timeshift/timeshift.json`
show your choices in the tool's own
spelling.

## Five rhythms

Timeshift schedules snapshots at five
rhythms: **boot**, **hourly**, **daily**,
**weekly** and **monthly**. Each is a
switch in the configuration —
`schedule_daily` and its siblings, set
to `"true"` or `"false"` — and each has
a partner number, its **count**:
`count_daily` says how many daily
snapshots to keep. The package template
the lab started from carries counts of
2 monthly, 3 weekly, 5 daily, 6 hourly
and 5 boot, with every schedule off;
a sensible settled setup turns on two
or three rhythms rather than all five.

The kinds matter beyond their timing.
Each snapshot wears its kind as a **tag**
— B, H, D, W, M, or O for the on-demand
snapshots you make by hand — and the
counts apply per kind. Keeping five
dailies does not protect your weeklies,
and on-demand snapshots are yours to
delete by hand; Timeshift does not prune
the ones you asked for explicitly.

## The hourly heartbeat — verified

Nothing in Timeshift runs all the time.
The rhythm is supplied by cron, and
this part the lab *can* show you,
because it happened on the lab machine.
When the lab ran `timeshift --check`
with a schedule switched on, Timeshift
installed its own cron file, and
transcript t05 quotes it from the disk:

```text
SHELL=/bin/bash
PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin
MAILTO=""

0 * * * * root timeshift --check --scripted
```

Once an hour, every hour, cron runs the
checker as root. `--check` reads the
configuration and the shelf, works out
which scheduled snapshots are due — a
daily is due when the newest daily is
more than a day old, and so on — and
makes those and no others. Then it
prunes: snapshots of each scheduled
kind beyond that kind's count are
deleted, oldest first. The whole of
scheduling is that loop, which is why
the configuration's switches and counts
are the whole of the setup too. If the
cron file is missing, no scheduled
snapshot will ever happen, whatever the
configuration says — worth checking
before you blame the counts.

## What the lab could not watch

The lab's checker ran, installed its
cron file, and then met the same empty
device list as every other command:
a snapshot *was* due — the configuration
asked for dailies and the shelf was
empty — and none could be made
(transcript t03). A checker that finds
its device, makes a due snapshot, and
prunes an over-count shelf is described
from Timeshift's documentation. The
division of labour, though, is not
documentation; it is there in the cron
file and the log of the run that
installed it.

## Choosing counts without drama

Counts are a space decision wearing a
calendar's clothes. Each kept snapshot
costs the changes since its neighbour
(chapter 6), so deep shelves are cheaper
than they look — but the *first* weeks
of a system's life change a great deal,
and a shelf on a small partition can
fill it, which is its own kind of
incident. The template's numbers are a
reasonable start. Whatever you choose,
verify once that the machine agrees:
`timeshift --list` shows the tags, and
the shelf should hold no more of each
letter than its count allows.
