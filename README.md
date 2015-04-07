# ZFS snapshot cleanup script

This is a simple script that cleans up old ZFS snapshots, designed to be
invoked as a periodic task in FreeBSD. It does not have any specific
requirements for snapshot naming.

## Installation

```
% sudo cp zfs-snapshot-cleanup /usr/local/etc/periodic/daily/
% echo 'zfs_snapshot_cleanup_enable=YES' | sudo tee -a /etc/periodic.conf
```

## Configuration

The script keeps a configured number of most recent snapshots and deletes
others, so even if script that creates the snapshot fails for some reason
you'll still have something.

To enable the cleanup for a dataset and its descendants simply set
`periodic:snapshots_to_keep` property for it:

```
% sudo zfs set periodic:snapshots_to_keep=10 mypool/some/dataset
```

After this for each dataset the script will delete all the snapshots except
10 the most recent ones. There are no requirements for snapshot names, it
uses `creation` property of each snapshot (set automatically by ZFS) to figure
out their age.

By default it will ignore snapshot that have names starting with 'daily',
'weekly' or 'monthly', to enable cleanup of those set a specific property,
e.g. `periodic:daily_snapshots_to_keep`.
