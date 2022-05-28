Buttery: A Btrfs Backup Tool
============================

`buttery` is a tool for managing backups of your [Btrfs](http://btrfs.wiki.kernel.org) subvolumes.

### Making backup snapshots

Say you have Btrfs subvolumes at `/` and `/home`, and you want to store backups in `/mnt/backups`:

    # buttery /mnt/backups / /home

This will result in a set of snapshots with links to the latest snapshots like this:

    /mnt/backups/root_20220102-124500
    /mnt/backups/root_latest -> root_20220102-124500
    /mnt/backups/home_20220102-124500
    /mnt/backups/home_latest -> home_20220102-124500

The next time you run this command, it will create another set of snapshots and update the `*_latest*` symlinks accordingly.

### Remote backup targets

`buttery` can also send backups to other Btrfs filesystems via `btrfs send` and `btrfs receive`.
The following command will create local backups of `/` and `/home` in `/mnt/backups`.
`buttery` will then `btrfs send` the created snapshots to a Btrfs filesystem on `/mnt/usb`:

    # buttery --remote=/mnt/usb /mnt/backups / /home

You can send snapshots to other machines by overriding the remote `btrfs` command:

    # buttery --remote-cmd='ssh root@backups.example.com btrfs' \
              --remote=/mnt/usb /mnt/backups / /home

When sending snapshots to a remote filesystem, `buttery` will also create `$NAME_latest_$REMOTENAME` symlinks.
This helps you keep track of what snapshots are on your remote filesystems.

### Purging old snapshots

After running `buttery` for a while, your snapshots will start piling up.
You can use `--purge` and it's related options to thin out old backups.
The following command would keep daily snapshots for a week, weekly snapshots for a month, and monthly snapshots for six months, and purge the rest:

    # buttery --purge --daily=7 --weekly=31 --monthly=184 /mnt/backups

Any snapshots pointed to by a `*_latest` symlink will not be purged, so you don't accidentally lose a remote parent snapshot.

You can also combine the purge-related options with your normal backup command to automatically clean up old snapshots after a normal backup.


Installation
============

I've included a set of simple autotools scripts, so you can use the usual process to install:

    $ ./autogen.sh && ./configure && make && sudo make install

If you don't want to use autotools, `buttery` is a single shell script, so you can just copy it somewhere if you know where you want to put it.

You can build the manual page with `asciidoc` via `a2x`:

    $ a2x -f manpage buttery.1.adoc

`buttery` is tested with [GNU `bash`][] and [BusyBox `sh`][], and requires [`btrfs-progs`][], and [`pv`][] at runtime.

  [GNU `bash`]: https://www.gnu.org/software/bash/bash.html
  [BusyBox `sh`]: https://busybox.net
  [`btrfs-progs`]: https://btrfs.wiki.kernel.org
  [`pv`]: http://www.ivarch.com/programs/pv.shtml


Contributing
============

Feel free to report issues or submit pull requests via [Github](https://github.com/ZDBioHazard/buttery).
