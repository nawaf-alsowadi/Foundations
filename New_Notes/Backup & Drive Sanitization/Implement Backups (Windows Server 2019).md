

This lab focused on **resilience and recovery** by implementing **backups** and **Volume Shadow Copy Service (VSS)** in Windows Server 2019. The goal is to learn how to **protect files, restore deleted data, and recover previous file versions**.

---

# 1. Preparing for Backup Operations

Before creating backups, a separate **storage device** was prepared to act as the **backup target**.

## Creating Backup Storage Using DiskPart

The lab used **Disk 1** (secondary unused drive) and configured it as drive **F:** labeled **Backup01**.

### DiskPart Commands Explained

**1. Start DiskPart**

```cmd
diskpart
```
- Opens Microsoft's **disk management** in **CLI tool**.
- Used to manage disks, partitions, and volumes.

**2. Select Disk 1**

```cmd
select disk 1
```
- Places DiskPart focus on **Disk 1**.
- Windows numbers disks starting from **0**:
    - Disk 0 = OS drive
    - Disk 1 = secondary drive

**3. Bring Disk Online**

```cmd
online disk
```
- Makes the disk available for use.
- Some newly attached disks may appear offline.

**4. Remove Read-Only Attribute**

```cmd
attribute disk clear readonly
```
- Enables writing to the disk (Removes write protection).

**5. Create Primary Partition**

```cmd
create partition primary
```
- Creates a partition using the full disk space.
- A partition becomes a usable **volume** after formatting.

**6. Format Using NTFS**

```cmd
format fs=ntfs label="Backup01" quick
```

| Part             | Purpose              |
| ---------------- | -------------------- |
| fs=ntfs          | Uses NTFS filesystem |
| label="Backup01" | Names the drive      |
| quick            | Fast formatting      |

NTFS is preferred because it supports:
- Permissions
- Compression
- Encryption
- Large file sizes

**7. Assign Drive Letter**

```cmd
assign letter=f
```
- Mounts/Name the volume as **F:**
 
# 2. Creating Sample Files

The lab created test files that would later be backed up and restored.

General syntax:

```cmd
copy <source> <destination>
```

Example:

```cmd
copy C:\SETUP\NetworkMiner\Fingerprints\oui.txt C:\Users\Jaime\Documents\document01.txt
```

This command:
- Copies a file
- Can rename the copied file simultaneously

Three files were created in **Documents**:
- document01.txt
- document02.txt
- document03.txt

One additional file:
- document04.txt
was placed inside:

```text
C:\Users\Public\
```

These served as **backup and restoration samples**.

# 3. Protecting Files Using Windows Server Backup

The next stage used **Windows Server Backup**.

## What is Windows Server Backup?

Windows Server Backup is a **built-in** Microsoft backup utility that performs:
- Full backups
- Custom backups
- Scheduled backups
- File restoration

The lab performed a **one-time backup**.

## Backup Configuration

The backup wizard was configured to:

### Backup Source

```text
C:\Users
```

### Backup Destination

```text
F:\ (Backup01)
```

### Backup Type

Custom → Users folder only

## Why Backup C:\Users?

Because it contains:
- User profiles
- Documents
- Desktop files
- Public files
    

This **minimizes storage** use compared to full system backup.

## Backup Process

The wizard:
1. Selected **Backup Once**
2. Used **Different Options**
3. Chose **Custom Backup**
4. Added:
```text
C:\Users
```
5. Selected:
```text
Local drives
```
6. Chose:
```text
Backup01 (F:)
```
7. Executed backup

The process generated a recoverable backup image.

## Backup Scheduling

The lab noted that **scheduled backups** are generally preferable. Why? Because one-time backups may quickly become outdated. Scheduled backups provide:
- Automatic protection
- Reduced human error
- Consistent recovery points

# 4. Deleting and Restoring a File

To test recovery, **document01.txt** was deleted.
Steps:
1. Delete file
2. Empty Recycle Bin

Emptying the Recycle Bin was important because it ensured:
- File removal became **permanent**
- Restoration required an actual backup

## Restoring from Windows Server Backup

Click on Recovery Wizard process:
### Recovery Source

```text
This server (PC10)
```

### Recovery Type

```text
Files and folders
```

### File Selected

```text
document01.txt
```

### Recovery Location

```text
Original location
```

After recovery:

```text
C:\Users\Jaime\Documents\document01.txt
```

reappeared successfully.

# Why Did Recovery Work?

Because backups contain:
- Copies of files
- Metadata
- Directory structure

Even after permanent deletion, the backup stored a recoverable copy. This demonstrates the key security principle: **Backups protect against permanent data loss.**

# 5. Volume Shadow Copy Service (VSS)

The second protection mechanism used was **Volume Shadow Copy Service**.

## What is VSS?

VSS creates **snapshots** of files and volumes.

Unlike traditional backups:

|Backup|VSS|
|---|---|
|Separate stored copy|Snapshot on same volume|
|Recovers deleted files|Restores earlier versions|
|Long-term protection|Short-term recovery|

Important distinction: **VSS is NOT a replacement for backups.**

# Enabling Shadow Copies

Using:

**Disk Management → C: Properties → Shadow Copies → Enable**

Once enabled:
- Windows creates shadow snapshots
- Previous versions become available

A snapshot was created immediately.

# Forcing a VSS Snapshot

The lab used **WMIC**.

Command:

```cmd
wmic shadowcopy call create Volume=c:\
```

Explanation:

|Component|Meaning|
|---|---|
|wmic|Windows Management Instrumentation CLI|
|shadowcopy|VSS subsystem|
|call create|Create snapshot|
|Volume=c:\|Snapshot C drive|

Expected result:

```text
Method execution successful
```

This forced VSS to create a restore point immediately.

# Modifying a File

The file:

```text
C:\Users\Public\document04.txt
```

was edited.

The word:

```text
changed
```

was inserted as the first line.

This simulated:
- Accidental modification
- Undesired edits
- Data corruption

# Restoring Previous Version with VSS

Using:

```text
Right-click → Restore previous versions
```

Windows displayed available snapshots.

Selecting **Restore** reverted:

document04.txt

to its earlier state.

Verification showed:
- "changed" line disappeared
- Original content returned

# Why Did VSS Recovery Work?

Because VSS stores:
- Point-in-time snapshots
- File state before modification

It compares: **Current file** vs **snapshot version** and restores the selected state.

# Backup vs VSS

This distinction is important.

| Feature               | Backup | VSS     |
| --------------------- | ------ | ------- |
| Restore deleted file  | Yes    | No      |
| Restore modified file | Yes    | Yes     |
| Separate storage      | Yes    | No      |
| Disaster recovery     | Yes    | Limited |
| Long-term retention   | Yes    | No      |

Key difference:
- **Backups = data loss protection**
- **VSS = version recovery**


# Integrated Explanation of Comprehensive Questions

## 1. If a file is deleted, what can restore it?

Correct answers:

✅ Backup  
✅ Volume Shadow Copy Service


## 2. If a file is modified, what can restore it?

Correct answers:

✅ Volume Shadow Copy Service  
✅ Backup

## 3. What is the best backup practice?

Correct answer:

✅ Store essential backups offsite.

Explanation:

Offsite backups protect against:
- Fire
- Flood
- Theft
- Ransomware
- Site disasters

Keeping backup and production systems together creates a **single point of failure**.

## 4. Most important factor when setting backup schedules?

Correct answer:

✅ How much data can be lost and still restore business functions.

Explanation:

This refers to: **Recovery Point Objective (RPO)**
RPO defines:
> Maximum acceptable data loss.

Example:

| RPO    | Backup Frequency |
| ------ | ---------------- |
| 24 hrs | Daily            |
| 1 hr   | Hourly           |

**Business tolerance drives schedule design.**

## 5. Besides backup and VSS, where can files be restored?

Correct answer:
✅ Recycle Bin

Explanation:
Recycle Bin stores:
- Recently deleted files
- Before permanent deletion

It offers:
- Fast local recovery
- No backup dependency

But once emptied, recovery requires backup or other mechanisms.

# Final Lab Takeaway

This lab demonstrated **three layers of Windows data recovery**:
1. **Recycle Bin**
- Recover recently deleted files

2. **Windows Server Backup**
- Recover permanently lost files
- Disaster recovery mechanism

1. **Volume Shadow Copy Service**
- Restore previous file versions
- Undo modifications

Together these mechanisms improve **resilience, availability, and recovery**, which are core objectives of security architecture and business continuity.