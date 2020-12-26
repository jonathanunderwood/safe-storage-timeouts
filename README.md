safe-storage-timeouts
=====================

This Ansible module adds a udev rule that attempts to set safe kernel driver timeouts for drives depending on whether they have SCTERC/TLER functionality enabled.

This is an attempt to address the issue of [drives dropping out of RAID arrays caused by incorrect timeouts](https://strugglers.net/~andy/blog/2015/11/09/linux-software-raid-and-drive-timeouts/).

An [earlier effort](https://github.com/jonathanunderwood/mdraid-safe-timeouts) at managing this situation identified only dnly those disks with redundant (raid1 or higher) mdraid partitions and set the timeout for those disks. However, as [Chris Murphy points out](https://www.spinics.net/lists/raid/msg60784.html) that approach is insufficient as the problem with incorrect timeouts also affects non-redundant disks and other RAID implementations such as BTRFS.

The approach taken with this module is to has a udev rule that sets the kernel driver timeout for each drive using the following logic: (1) If the drive has SCTERC enabled, then set the kernel timeout to be around 5 secs more than the SCTERC read timeout; (2) If the drive has SCTERC functionality disabled, or SCTERC functionality is not present, set the kernel timeout to 180 secs.


Requirements
------------

This role will install the [smartmontools](https://www.smartmontools.org/) package using the operating system package manager. The role requires a version of smartmontools recent enough to support the `--json` command line option of the `smartctl` command.


Role Variables
--------------

| Variable                 | Default               | Description                                                 |
| :--                      | :--                   | :--                                                         |
| `helper_script_dir`      | `/usr/local/lib/udev` | The location to install the required udev helper script to. |
| `smartmontools_pkg_name` | `smartmontools`       | The distribution package name for smartmontools             |


Dependencies
------------

None

Example Playbook
----------------

    - hosts: servers
      roles:
        - role: safe-storage-timeouts


License
-------

GPL 3.0+

Author Information
------------------

Jonathan G. Underwood

Further Reading
---------------

- https://strugglers.net/~andy/blog/2015/11/09/linux-software-raid-and-drive-timeouts/
- https://www.spinics.net/lists/linux-btrfs/msg41211.html
- https://forum.openmediavault.org/index.php?thread/25398-raid-smart-timeout-stcerc-and-drives-how-to-set-them-correctly-in-omv/
- https://en.wikipedia.org/wiki/Error_recovery_control

