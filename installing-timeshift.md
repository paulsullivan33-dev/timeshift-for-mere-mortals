# Installing Timeshift

**Outcome: Timeshift is installed, and
two checks pass — it reports its
version, and it can see at least one
device to work with.** The second check
is the one this lab failed, and this
chapter shows both outcomes so you can
tell them apart on your own machine.

## The ordinary install

On Ubuntu and Debian, Timeshift is one
package:

```bash
sudo apt update
sudo apt install timeshift
```

Linux Mint includes it already. Other
distributions carry it too — Fedora and
Arch both package it — and the project
publishes the source for everyone else;
those routes are described from the
documentation and were not the lab's.
The package pulls in what it needs: the
dependency list the lab recorded
(transcript t06) shows rsync, a cron
daemon, psmisc and the GTK libraries for
the window, among others. Notice the
cron daemon in that list — chapter 7's
hourly checker is not an optional extra;
it is part of the design.

## How the lab installed it, honestly

This guide's lab could not run a normal
package-manager install: the sandbox's
package state is shared with other work.
So the lab did the same download apt
would do — the timeshift package and
the eighteen not-yet-installed packages
around it, from Ubuntu's noble archive —
and unpacked the files with `dpkg -x`
into a private tree, running the binary
from there, unmodified. Nothing about
the program changed; only the furniture
around it. The version below is the real
program answering. The lab notes in
`criteria.md` record the details, and
the download itself is transcript t00.

## Check one: the version

```bash
timeshift --version
```

The lab's answer, verbatim:

```text
Timeshift 24.01.1
```

Any version number at all means the
program is present and runs. If the
shell answers "command not found"
instead, the install did not happen —
go back to the package step rather than
pressing on.

## Check two: the device list

This is the check that decides whether
Timeshift can work on your machine at
all:

```bash
sudo timeshift --list-devices
```

On an ordinary machine this prints a
table headed "Devices with Linux file
systems", with a row for each partition
Timeshift could hold snapshots on: its
device name, size, type and label. One
row is enough. You will pick from those
rows in chapter 5.

The lab's run of the same command is
transcript t02, and it is worth looking
at before you need it. The table came
back **empty** — header, rule, nothing
under it — preceded by one quiet line of
explanation:

```text
E: Error opening directory '/dev/mapper': No such file or directory
```

The lab machine's kernel can name some
virtual disks, but it has no block
device files in `/dev`, and the tools
Timeshift uses to inventory filesystems
— `blkid` above all — found nothing:
run by hand, `blkid` printed nothing at
all, and `lsblk -f` showed every device
with blank filesystem and UUID columns
(transcript t00). No filesystem, no
UUID, no row in the table, and — as the
next chapters show — no snapshot, ever,
until that changes. Virtual machines
with their disks properly attached and
ordinary hardware do not have this
problem; sealed sandboxes and minimal
containers do. If your table is empty,
chapter 14 is your chapter, and no
amount of configuration will substitute
for a device the machine can see.

## A note about root

Every Timeshift command in this guide
runs as root, through `sudo`. That is
not ceremony: the program copies and
restores the entire system, mounts
devices, and writes its configuration
in `/etc`. A command that fails with a
permission complaint has usually just
been run without it.
