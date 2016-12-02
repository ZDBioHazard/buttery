Buttery: A Btrfs Backup Tool
============================

`buttery` is a tool for managing backups of your [Btrfs](http://btrfs.wiki.kernel.org) subvolumes.


What Does it Do?
================

Say you have Btrfs subvolumes at `/`, `/home`, and `/mnt/media`, and you want to store backups in `/mnt/backups`:

    # buttery /mnt/backups / /home /mnt/media

This will result in a set of snapshots with links to the latest snapshots like this:

    /mnt/backups/root_2017-01-01_00-00-00
    /mnt/backups/root_latest  ->  root_2017-01-01_00-00-00
    /mnt/backups/home_2017-01-01_00-00-00
    /mnt/backups/home_latest  ->  home_2017-01-01_00-00-00
    /mnt/backups/media_2017-01-01_00-00-00
    /mnt/backups/media_latest  ->  media_2017-01-01_00-00-00

The next time you run that command, it will create another set of snapshots and update the `latest_*` symlinks accordingly.

`buttery` can also send backups to other Btrfs filesystems via `btrfs send` and `btrfs receive`.
The following command will create local backups in `/mnt/backups` and `btrfs send` the created snapshots to a Btrfs filesystem on `/mnt/usb`:

    # buttery --remote=/mnt/usb /mnt/backups / /home /mnt/media

You can send snapshots to other machines by overriding the remote `btrfs` command:

    # buttery --remote-cmd='ssh root@backups.example.com btrfs' \
              --remote=/mnt/usb /mnt/backups / /home /mnt/media

After running `buttery` for a while, your snapshots will start piling up.
You can use `--purge` and it's related options to thin out old backups.
This command would keep daily snapshots for a week, weekly snapshots for a month, and monthly snapshots for six months:

    # buttery --purge --daily=7 --weekly=31 --monthly=184 /mnt/backups

You can also combine the purge-related options with your normal backup command to automatically clean up old backups after a normal backup.

Be sure to check out the [manual page](buttery.1.adoc) for more information about the options and how to use them.


Installing
==========

I've included a set of simple autotools scripts, so you can use the usual process to install:

    $ ./autogen.sh && ./configure && make && sudo make install

If you don't want to use autotools, `buttery` is a single GNU `bash` script, so you can just copy it somewhere if you know where you want to put it.

You can build the manual page with `asciidoc` via `a2x`:

    $ a2x -f manpage buttery.1.adoc

`buttery` depends on [GNU `bash`](https://www.gnu.org/software/bash/bash.html), [`btrfs-progs`](https://btrfs.wiki.kernel.org), and [`pv`](http://www.ivarch.com/programs/pv.shtml)


Compatibility
=============

`buttery` is a GNU `bash` shell script that targets the GNU/glibc userspace.

In the future, I intend to target both GNU/glibc and [busybox](https://www.busybox.net)/[musl libc](http://www.musl-libc.org) for [Alpine Linux](https://alpinelinux.org).


Contributing
============

Feel free to report issues or submit pull requests via [Github](https://github.com/ZDBioHazard/buttery).
