# 在 /dev/vdb 上创建并激活 swap 分区

In this step, you will create a swap partition on the `/dev/vdb` disk. Swap space is a portion of a hard disk drive (HDD) or solid-state drive (SSD) used for temporary storage when the system runs out of physical RAM. It acts as an overflow for RAM, allowing the system to continue operating even when memory is scarce, though at a slower speed.

First, let's inspect the current partition table on `/dev/vdb` to determine where to create the new swap partition.

```bash
parted /dev/vdb print
```

You should see the existing XFS partition (`/dev/vdb1`) that you created in the previous step:

```plaintext
Model: Virtio Block Device (virtblk)
Disk /dev/vdb: 5369MB
Sector size (logical/physical): 512B/512B
Partition Table: msdos
Disk Flags:

Number  Start   End     Size    Type     File system  Flags
 1      1049kB  1001MB  1000MB  primary  xfs
```

Now, you will add a new primary partition of 500 MB for use as swap space. You will set the partition file-system type to `linux-swap`. The new partition will start immediately after the existing `/dev/vdb1` partition. The end of `/dev/vdb1` is at `1001MB`. So, the new partition will start at `1001MB` and end at `1501MB` (1001MB + 500MB).

Use `parted` in non-interactive mode to create this partition:

```bash
parted /dev/vdb mkpart primary linux-swap 1001MB 1501MB
```

You might see the `Information: You may need to update /etc/fstab.` message again.

Verify your work by listing the partitions on the `/dev/vdb` disk:

```bash
parted /dev/vdb print
```

You should now see two partitions, with the second one being your new swap partition:

```plaintext
Model: Virtio Block Device (virtblk)
Disk /dev/vdb: 42.9GB
Sector size (logical/physical): 512B/512B
Partition Table: msdos
Disk Flags:

Number  Start   End     Size    Type     File system  Flags
 1      1049kB  1001MB  1000MB  primary  xfs
 2      1001MB  1501MB  499MB   primary               swap
```

As before, after creating a new partition, you must run `udevadm settle` to ensure the system registers the new partition and creates its device file (`/dev/vdb2`).

```bash
udevadm settle
```

Now, format the new partition (`/dev/vdb2`) as swap space using the `mkswap` command. This command initializes the partition for use as swap.

```bash
mkswap /dev/vdb2
```

The output will show details about the swap space creation, including its size and a generated UUID:

```plaintext
Setting up swapspace version 1, size = 476 MiB (499118080 bytes)
no label, UUID=4379b167-ab39-4c83-bf7c-b28fbdb38725
```

To configure the new swap space to activate persistently, you need to add an entry to the `/etc/fstab` file. First, discover the UUID of the `/dev/vdb2` device.

```bash
lsblk -o UUID /dev/vdb2
```

Note down the UUID from the output. It will be similar to `4379b167-ab39-4c83-bf7c-b28fbdb38725`.

```plaintext
UUID
4379b167-ab39-4c83-bf7c-b28fbdb38725
```

Open the `/etc/fstab` file using `nano` and add a new line for your swap partition. Replace `YOUR_SWAP_UUID_HERE` with the actual UUID you just found.

```bash
nano /etc/fstab
```

Add the following line to the end of the file:

```text
UUID=YOUR_SWAP_UUID_HERE swap swap defaults 0 0
```

**Explanation of the `/etc/fstab` entry for swap:**

- `UUID=YOUR_SWAP_UUID_HERE`: Specifies the device to use as swap.
- `swap`: The mount point (for swap, this is always `swap`).
- `swap`: The file system type (for swap, this is always `swap`).
- `defaults`: Standard options for swap.
- `0`: `dump` option (0 means no dump).
- `0`: `fsck` order (0 means no fsck check for swap).

Save the file by pressing `Ctrl+X`, then `Y` to confirm, and `Enter` to write to the file.

After modifying `/etc/fstab`, reload the `systemd` daemon to recognize the new entry.

```bash
systemctl daemon-reload
```

Finally, enable the swap space using the `swapon -a` command. The `-a` option tells `swapon` to enable all swap devices listed in `/etc/fstab`.

```bash
swapon -a
```

Verify that the new swap space is enabled using `swapon --show`:

```bash
swapon --show
```

You should see output similar to this, confirming your new swap partition is active:

```plaintext
NAME      TYPE      SIZE USED PRIO
/dev/vdb2 partition 476M   0B   -2
```

The output shows your newly created swap partition is active and ready to use.
