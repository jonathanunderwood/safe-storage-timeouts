safe-storage-timeouts
=====================

This Ansible module adds a udev rule that attempts to set safe kernel driver timeouts for drives depending on whether they have SCTERC/TLER functionality enabled.

This is an attempt to address the issue of [drives dropping out of RAID arrays caused by incorrect timeouts](https://strugglers.net/~andy/blog/2015/11/09/linux-software-raid-and-drive-timeouts/).

An [earlier effort](https://github.com/jonathanunderwood/mdraid-safe-timeouts) at managing this situation identified only dnly those disks with redundant (raid1 or higher) mdraid partitions and set the timeout for those disks. However, as [Chris Murphy points out](https://www.spinics.net/lists/raid/msg60784.html) that approach is insufficient as the problem with incorrect timeouts also affects non-redundant disks and other RAID implementations such as BTRFS.

The approach taken with this module is to has a udev rule that sets the kernel driver timeout for each drive using the following logic: 

1. If the drive has SCTERC enabled, then set the kernel timeout to be around 5 secs more than the SCTERC read timeout;2. Otherwise, if the drive has SCTERC functionality disabled, attempt to activate it and set a suitable device read and write timeout of 7 seconds
3. If activating SCTERC fails or if SCTERC functionality is not present, set the kernel timeout to 180 secs.


Requirements
------------

This role will install the [smartmontools](https://www.smartmontools.org/) package using the operating system package manager. The role requires a version of smartmontools recent enough to support the `--json` command line option of the `smartctl` command.


Role Variables
--------------

| Variable                 | Default               | Description                                                                                              |
| :--                      | :--                   | :--                                                                                                      |
| `helper_script_dir`      | `/usr/local/lib/udev` | The location to install the required udev helper script to.                                              |
| `smartmontools_pkg_name` | `smartmontools`       | The distribution package name for smartmontools                                                          |
| `devices`                | `[]` (empty list)     | A list of devices to apply the rules too. If the list is empty, the rules will be applied to all devices |


Dependencies
------------

None

Example Playbook
----------------

    - hosts: servers
      roles:
        - role: safe-storage-timeouts


Possible Improvements
---------------------

1. Expose the device and kernel timeouts as variables that can be set. As a first step this would be values applied to all devices, and as a follow on, it might be useful to be able to set values per device.
2. Testing: [molecule](https://molecule.readthedocs.io/en/latest/) is set up and configured for testing, and at present checks that the role successfully runs with default values, and also lints the code. Expanding the testing would be valuable for the future.


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

