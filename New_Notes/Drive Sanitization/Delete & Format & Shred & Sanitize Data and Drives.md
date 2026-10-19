# Assisted Lab: Performing Drive Sanitization

## Objective

Learn how data destruction and drive sanitization work in Linux. This lab demonstrates the difference between deletion, formatting, shredding, and full drive sanitization, and shows why deleted or formatted data may still be recoverable.

**CompTIA Security+ Objective:**
- 4.2 Explain the security implications of proper hardware, software, and data asset management.

# 1. Prep a Portable Drive for Use

## Objective

Create a partition on a new 80 GiB storage device, format it using FAT32, mount it, and copy files to it.

### Identify Storage Devices

```bash
fdisk -l
```

| Element | Meaning                                  |
| ------- | ---------------------------------------- |
| fdisk   | Disk partition utility                   |
| -l      | Lists all storage devices and partitions |

Purpose:
- Display connected drives.
- Identify the 80 GiB drive with no partitions.

Example:

```bash
/dev/sdb
```

---

### Open Disk Partition Utility

```bash
fdisk /dev/sdb
```

|Element|Meaning|
|---|---|
|fdisk|Partition manager|
|/dev/sdb|Target drive|

Opens interactive partition editor.

---

### Display Help

```bash
m
```

Displays all fdisk commands.

Common commands:

|Command|Meaning|
|---|---|
|m|Help|
|n|New partition|
|p|Print partition table|
|v|Verify partition table|
|w|Write changes|
|q|Quit|

---

### View Current Partition Table

```bash
p
```

Shows existing partitions.

Result:
- No partition table entries.
- Entire disk unallocated.

---

### Verify Disk Structure

```bash
v
```

Checks partition table integrity.

Result:
- No errors.
- All sectors unallocated.

---

### Create New Partition

```bash
n
```

Responses:

```bash
p
1
<Enter>
<Enter>
```

| Response | Meaning                                                                                                            |
| -------- | ------------------------------------------------------------------------------------------------------------------ |
| p        | Primary partition (e for extended and used for logical partition for containers)                                   |
| 1        | Partition number (/dev/sdb1)                                                                                       |
| Enter    | Accept default first sector (2048 cause 0-2048 reserved for boot, performance optimization, ... etc)               |
| Enter    | Accept default last sector (default is the last sector of available drive, 100% of drive will be used for default) |

Result:

```bash
/ dev/sdb1
```

created using all free space.

---

### Confirm Partition

```bash
p
```

Shows:

```bash
/dev/sdb1
```

---

### Write Changes

```bash
w
```

Writes partition table to disk and exits fdisk.

---

### Format Partition

```bash
mkfs -t fat /dev/sdb1
```

|Element|Meaning|
|---|---|
|mkfs|Make filesystem|
|-t|Filesystem type|
|fat|FAT32|
|/dev/sdb1|Target partition|

Purpose:
- Create FAT32 filesystem.
- Allow compatibility across operating systems.

---

### Create Mount Point

```bash
mkdir /mnt/SalesStorage
```

| Element           | Meaning          |
| ----------------- | ---------------- |
| mkdir             | Create directory |
| /mnt/SalesStorage | Mount location   |

---

### Mount Filesystem

```bash
mount /dev/sdb1 /mnt/SalesStorage
```

| Element           | Meaning           |
| ----------------- | ----------------- |
| mount             | Attach filesystem |
| /dev/sdb1         | Partition         |
| /mnt/SalesStorage | Mount point       |

---

### Verify Mount

```bash
ls -l /mnt/SalesStorage
```

Initially:

```bash
total 0
```

---

### Copy Files

```bash
cp -r /usr/share/seclists/* /mnt/SalesStorage/
```

| Element | Meaning                 |
| ------- | ----------------------- |
| cp      | Copy                    |
| -r      | Recursive               |
| *       | Everything in directory |

Copies all SecLists data onto storage.

---

### Verify Files

```bash
ls -l /mnt/SalesStorage
```

Displays copied folders and files.

---

# 2. Delete and Undelete File Objects

## Objective

Delete a directory and recover it using TestDisk.

### Delete Directory

```bash
rm -r /mnt/SalesStorage/Miscellaneous
```

|Element|Meaning|
|---|---|
|rm|Remove|
|-r|Recursive|

Deletes directory and contents.

---

### Verify Deletion

```bash
ls -l /mnt/SalesStorage
```

Result:

```bash
Miscellaneous
```

no longer exists.

---

### Attempt Recovery

```bash
testdisk /dev/sdb1
```

| Element   | Meaning          |
| --------- | ---------------- |
| testdisk  | Recovery utility |
| /dev/sdb1 | Target partition |

TestDisk navigation:
1. Proceed
2. Select **None**
3. Select **Undelete**
4. Highlight **Miscellaneous**
5. Press `:`
6. Press `C`
7. Choose Downloads
8. Press `C`

Result:

```text
Copy done!
```

---

### Verify Recovery

```bash
ls -l Downloads/Miscellaneous
```

Recovered files should appear.

---

### Terminal Reset (if needed)

```bash
reset
```

Restores normal terminal display.

---

## Important Concept

Deletion only:
- Removes references.
- **Marks storage space reusable.**

Deletion does **not** destroy data.
Deleted data may still be recoverable.

---

# 3. Securely Delete Files Using shred

## Objective

Overwrite file contents before deletion so recovered files contain no usable data.

---

### View Password Files

```bash
ls -lR /mnt/SalesStorage/Passwords
```

|Element|Meaning|
|---|---|
|-R|Recursive listing|

Shows all files and subdirectories.

---

### Securely Delete Files

```bash
cat /mnt/SalesStorage/Passwords/bt4-password.txt
```
```bash
find /mnt/SalesStorage/Passwords -type f -exec shred -uvz {} \;
```

Breakdown:

| Component | Meaning                    |
| --------- | -------------------------- |
| find      | Locate files               |
| -type f   | Files only                 |
| -exec     | Run command                |
| shred     | Secure overwrite           |
| -u        | Remove after overwrite     |
| -v        | Verbose output             |
| -z        | Final overwrite with zeros |
| {}        | Current filename           |
| ;         | End command                |

Purpose:
- Overwrite contents.
- Zeroize file.
- Delete file.

---

### Verify Shredding

```bash
ls -lR /mnt/SalesStorage/Passwords
```

Directories remain but files are gone.

---

### Delete Empty Directories

```bash
rm -r /mnt/SalesStorage/Passwords
```

---

### Attempt Recovery

```bash
testdisk /dev/sdb1
```

Recover Passwords folder using same process as before.

---

### Verify Recovered Files

```bash
ls -l Downloads/Passwords
```

Files may appear recovered.

---

### Check Contents

```bash
cat Downloads/Passwords/bt4-password.txt
```

Result:
- Empty file.

Meaning:
- Metadata recovered.
- Contents destroyed.

---

## Important Concept

Difference:

| Method | Result             |
| ------ | ------------------ |
| rm     | Deletes references |
| shred  | Overwrites data    |

Shred destroys contents before deletion.

---

# 4. Format a Drive to Destroy Data

## Objective

Format a partition and determine whether recovery is still possible.

### Unmount Filesystem

```bash
umount /mnt/SalesStorage
```

|Element|Meaning|
|---|---|
|umount|Unmount filesystem|

Note:

Correct command is:

```bash
umount
```

not:

```bash
unmount
```

---

### Format Partition

```bash
mkfs -t fat /dev/sdb1
```

Creates new FAT filesystem.

Formatting:
- Creates new filesystem structures.
- Creates new allocation tables.

Formatting does **not necessarily overwrite data**.

---

### Attempt Recovery

```bash
testdisk /dev/sdb1
```

Navigation:
1. Proceed
2. None
3. Undelete

Expected result:

```text
No file found
Filesystem may be damaged
```

---

## Important Concept

Formatting:
- Rebuilds filesystem.
- Removes filesystem metadata.

Formatting is **not secure sanitization**.
Some recovery tools may still recover data.

---

# 5. Sanitization of a Drive

## Objective

Perform true drive sanitization by overwriting the entire storage device.

### Re-Mount Storage

```bash
mount /dev/sdb1 /mnt/SalesStorage
```

---

### Re-Copy Data

```bash
cp -r /usr/share/seclists/* /mnt/SalesStorage/
```

Places data back on drive before destruction.

---

### Zeroize Entire Drive

```bash
dd if=/dev/zero of=/dev/sdb bs=1M status=progress
```

Breakdown:

| Component       | Meaning                  |
| --------------- | ------------------------ |
| dd              | Disk duplicator          |
| if              | Input file               |
| /dev/zero       | Infinite stream of zeros |
| of              | Output file              |
| /dev/sdb        | Entire drive             |
| bs=1M           | 1 MB blocks              |
| status=progress | Show progress            |

Purpose:
Overwrite entire disk with zeros.

Destroys:
- Files
- Directories
- Filesystems
- Partition tables
- Data remnants

---

### Alternative Method

```bash
dd if=/dev/random of=/dev/sdb bs=1M status=progress
```

Uses random data instead of zeros.

More secure but slower.

---

### Inspect Drive

```bash
fdisk /dev/sdb
```

---

### Print Partition Table

```bash
p
```

No partitions should exist.

---

### Verify Disk

```bash
v
```

Result:
- No errors.
- All sectors unallocated.

---

### Attempt Recovery

```bash
testdisk /dev/sdb
```

Navigation:
1. Proceed
2. Intel
3. Analyse

Expected result:

```text
No partitions found
```

Meaning:
Sanitization successful.

---

## Important Concept

Sanitization:
- Overwrites **storage** sectors.
- Destroys data remnants.
- Prevents forensic recovery.

Unlike formatting, sanitization destroys the underlying data.

---

# Data Destruction Comparison

| Method          | Removes References | Overwrites Data    | Secure  |
| --------------- | ------------------ | ------------------ | ------- |
| Delete (rm)     | Yes                | No                 | No      |
| Format (mkfs)   | Yes                | Usually No         | Limited |
| Shred           | Yes                | Yes                | Good    |
| dd Sanitization | Yes                | Yes (Entire Drive) | Best    |

---

# Key Security Concepts

## Data Remnants

Recoverable information remaining after deletion or formatting.

---

## Zeroization

Overwriting storage using zeros.

Example:

```bash
dd if=/dev/zero of=/dev/sdb
```

---

## Overwriting

Replacing original data with new data.

Most common sanitization method.

---

## Secure Deletion

Overwrites data before deleting files.

Example:

```bash
shred
```

---

## Sanitization

Complete destruction of recoverable data.

Example:

```bash
dd
```

---

# Comprehensive Questions

### What is the primary means by which data can be sanitized?

✅ Overwriting

---

### When files are no longer visible in a directory listing, the data is not recoverable.

❌ False

Deleted data may still be recoverable.

---

### What does deletion do to a file?

✅ Labels its clusters or blocks as available for reuse.

---

### Which of the following are common forms of sanitization?

✅ Zeroization

✅ Writing random data

---

### What is the term used to reference material on a storage device that might be recoverable?

✅ Remnants
