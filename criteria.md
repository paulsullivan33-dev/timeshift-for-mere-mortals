# Writing criteria for this guide

Every "for Mere Mortals" guide is written to the same nine-point
standard, so a reader who trusts one can trust the rest. Here it
is, with the verification notes for this particular guide below.

1. **Fix it first.** Reader outcomes lead, not action items: each
   chapter opens by naming the outcome it delivers, shows a check
   the reader can run to confirm they got it, and a reasonable
   reader finishes able to state what was done, what it means,
   and what to try next.
2. **Plain language.** Short sentences, everyday vocabulary,
   technical terms defined on first use. No slang, and no
   idioms that only make sense in one country — the reader may
   be translating as they go.
3. **No dead ends.** A guide never tells the reader to do
   something it cannot show them how to verify. Every claim a
   reader relies on — an install, a command, an output — is
   something this guide demonstrates or clearly labels
   documented-only below.
4. **Live-verified where it matters.** Procedures are run for
   real in the guide's lab wherever the build machine allows it,
   and the transcripts kept in the repository are the evidence.
   What could not be run is listed honestly below, never
   presented as if it had been.
5. **General audiences.** Nothing in this guide — examples,
   fixtures, names, addresses — is modelled on any particular
   reader or author's real life, network, or organisations.
   The lab's fixtures are the package's own shipped defaults
   and one freshly generated UUID that names nothing.
6. **Standalone.** A reader needs this guide, its lab, and
   nothing else. Where another guide would genuinely help, it
   is pointed to once, by name, and the chapter still completes
   without it.
7. **Honest about limits.** Chapter 13 names what the tool
   cannot do: it is not a backup, it does not hold your home
   directory's files by default, BTRFS mode demands one strict
   layout, the first rsync snapshot is a whole copy, and the
   tool cannot work at all on a machine with no discoverable
   block device — the wall this guide's own lab hit.
8. **The safety rule is in the text, early.** For Timeshift the
   rule is twofold and chapter 1 states both halves before any
   procedure: a snapshot is not a backup (the shelf shares the
   machine's fate), and a restore undoes every system change
   made since the snapshot, good and bad alike.
9. **Builds clean.** The site builds with MkDocs in strict mode
   with no warnings, and a fresh reader following the chapters
   in order reaches the stated outcome of each.

## Verification notes for this guide

The build machine is a 2-CPU, 7.7 GB Ubuntu 24.04.5 sandbox,
shared with other work while the lab ran, running as root. Its
decisive property: **it has no usable block devices.** The
kernel's `lsblk` can name virtual disks, but `/dev` contains no
block device files at all, `blkid` prints nothing (exit 2),
`lsblk -f` shows blank filesystem and UUID columns for every
device, and the root filesystem is an overlay, not a partition.
Timeshift found exactly that and behaved accordingly, and this
guide's centre of evidence is the refusal that followed —
never worked around, never dressed up.

Timeshift **24.01.1** (Ubuntu package 24.01.1-1build2, from
the noble archive) was run **unmodified**. The sandbox's
package state is shared, so the lab downloaded the package and
its not-yet-installed dependency closure (18 .debs) with apt
against lab-private package lists and unpacked the files with
`dpkg -x` into a private tree, putting that tree's libraries on
the library path to run the binaries. The ordinary `apt
install timeshift` route the guide teaches was therefore not
itself run here; it is the route the downloaded files came
from, and the dependency list it resolves is quoted from the
real package in transcript t06.

All transcripts live in `timeshift-lab/transcripts/`. The
configuration the lab used is `timeshift-lab/timeshift.json`,
and the template the package itself ships is
`timeshift-lab/default.json`.

Run for real, with transcripts as evidence:

- The machine's device reality, recorded before Timeshift was
  involved: `/dev` contents, `lsblk`, `blkid` (no output,
  exit 2), `lsblk -f` (no filesystem or UUID anywhere), the
  overlay root from `findmnt` (t00).
- `timeshift --version` answering "Timeshift 24.01.1", and the
  full `--help` text of the console tool (t00, t01).
- First run with no configuration: the JSON assertion warnings
  a missing config file causes, the **empty** "Devices with
  Linux file systems" table from `--list-devices`, `--check`
  falling into the interactive "Select backup device" prompt
  over an empty list and aborting after three attempts, and
  `--list` answering "Device : Not Selected" and "No snapshots
  found" (t02).
- The shipped `/etc/timeshift/default.json` read in full, and
  a configuration built from it for RSYNC mode — daily
  schedule on, `do_first_run` off, backup and parent UUID set
  to a freshly generated UUID that names no device on this
  machine, because no device here carries a UUID (t03).
- With that configuration: `--check` still fell into the
  device prompt (an unresolvable UUID is the same as no
  selection) but on its way out **installed the real cron
  file** `/etc/cron.d/timeshift-hourly` — `0 * * * * root
  timeshift --check --scripted` — which is quoted verbatim
  (t03, t05).
- `--create` both ways: interactively it aborts at the device
  prompt; with `--scripted` it refuses in two lines —
  "Snapshot device not available" / "Device not found:
  UUID='…'" — exit 1. The run's own log in
  `/var/log/timeshift/` shows the configuration loading, the
  device being set from the config file, and the failure point
  (t04).
- BTRFS mode's create attempt: "E: System disk not found!",
  exit 1 — the overlay root is no Btrfs system disk (t05).
- `--delete-all` against the empty shelf: the same device
  refusal before any deletion logic (t05).
- The state afterwards: no `/timeshift` directory anywhere,
  an empty `/run/timeshift` working directory created by the
  tool, and the per-run logs accumulating in
  `/var/log/timeshift/` (t05, t06). `timeshift-gtk` answered
  "cannot open display", as a GUI must on this machine (t05).
- The package's own dependency list (`dpkg-deb -f`), showing
  rsync, a cron daemon and psmisc among the requirements, and
  the system rsync's version (t06).

**No snapshot was created in this lab, and no restore was
attempted** — creating one was impossible here, and a restore
against the sandbox's real system files would have been out of
bounds even with a snapshot in hand. The lab's writes outside
its own tree were the tool's own and one hand-written config:
`/etc/timeshift/timeshift.json` (written from the package's
shipped default, and copied into this repository), the cron
file, logs and working directory Timeshift made for itself.
After the transcripts were captured, the cron file and the
config were removed again so the shared machine keeps no live
Timeshift schedule pointing at a program it does not have
installed; the transcripts preserve both verbatim.

Documented only — described from the project's documentation,
not run, and the text does not claim otherwise:

- Everything past a successful device selection: a completed
  snapshot in either mode, the shelf's on-disk layout
  (`timeshift/snapshots/<name>/<host>/…` and `info.json`),
  listing a shelf that holds snapshots, pruning by count.
- Restoring, in all its forms: from the running system, with
  `--snapshot`/`--target` named, the GRUB reinstall choice,
  and the live-media procedure for a machine that will not
  boot. Chapter 8 is labelled documented-only in its own text.
- Fetching a single file out of a snapshot by hand (chapter
  9): the layout it walks is the documented one; the lab had
  no shelf to reach into.
- The window (`timeshift-gtk`): the first-run wizard, the
  settings screens, restore by clicking. The binary's only
  lab appearance is its "cannot open display" warning.
- Successful scheduling: a checker that finds its device,
  makes a due snapshot and prunes. The lab verified the cron
  file, the configuration fields and the checker's attempt;
  the success path is documentation.
- The built-in exclude list's exact membership, described in
  chapter 10 in the terms the documentation uses. (The lab
  log does show the program running its
  `add_default_exclude_entries()` step before the device
  stage.)
- The neighbour tools of chapter 12 — characterised, not run
  or benchmarked. The rsync guide in this series ran the
  `--link-dest` mechanics for real; chapter 6 points there for
  the proof this lab could not repeat.

Fixtures are this guide's own and deliberately generic: the
package's shipped default configuration, a generated UUID
that names nothing, and stock example wording for snapshot
comments. No real machine, person or organisation appears.
