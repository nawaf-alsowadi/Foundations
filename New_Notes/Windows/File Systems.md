
Standards for organizing data on storage devices (SSD, HDD, USB, Hard Drive) and applied when we format a drive or partition.

It basically divide the storage devices into smaller compartments (rooms) knows as clusters. It also maintain **index** for where files are stored and the available compartments. 

The type of the file system **depends on the drive and OS.**
## File Allocation Table (FAT)
- Major variants = FAT12, FAT16 & FAT32.
- Each has an increasing number of clusters, and maximum file and volume sizes.
- FAT32 is the most popular because of its compatibility.
- FAT is still widely used for removable media

| File System | Max File Size             | Max Volume Size                                                           |
| ----------- | ------------------------- | ------------------------------------------------------------------------- |
| **FAT12**   | **32MB (8KB clusters)\*** | **32MB (8KB clusters)\***                                                 |
| **FAT16**   | **2GB / 4GB\*\***         | **16GB (256KB clusters)\*\***                                             |
| **FAT32**   | **4GB**                   | **32GB (Windows format)**<br>**2TB (other OS)**<br>**16TB (theoretical)** |

\* *16MB with 4KB clusters*  
\*\* *Different FAT16 versions were limited to 2GB, 4GB, 8GB or 16GB volumes depending on cluster & sector size.*

## New technology file system (NTFS) (Windows File Systems)

- File size limit of **16 exabytes (EB)**.
- **Journaling file system** (record any modifications/changes so it can recover from system crash/failure , to avoid corruption).
- Supports **file permissions** (RO) and **encryption**.
- **Windows must be installed on an NTFS drive**.
- Downside? **Limited non-Windows OS compatibility** — e.g., read-only in macOS and older Linux distros and may not be readable at all in any other devices such as standalone media players

*In case of a failure, the file system can automatically repair the folders/files on disk using information stored in a log file. This function is not possible with FAT.*

[NTFS Permissions By Microsoft](https://learn.microsoft.com/en-us/previous-versions/windows/it-pro/windows-2000-server/bb727008(v=technet.10)?redirectedfrom=MSDN)
![[Pasted image 20251206002755.png]]
![[Pasted image 20251206004041.png]]

#### **Alternate Data Streams** (**ADS**)
A feature that exist in NTFS that allow to store hidden data in files. Every file has one data stream *$DATA* and multiple alternate data streams. More simply, it allows a single file to contain **multiple data streams**. It is helpful in cases like hen we download a file from internet and the flag (internet download) will be stored using ADS to identify that the file was downloaded from the Internet.

- **Simple meaning:**  
  A file can secretly store **extra hidden data** inside it without changing its size or name.
- **Example:**  
  You can attach hidden text to a file like this:
	- notepad normalfile.txt:hidden.txt
- **Security Risks**
	- Malware can hide data inside ADS  
	- Hidden streams don’t show up in File Explorer  
	- Antivirus may not always detect ADS content
- **How to view ADS:**
	- Using `dir /r` in Windows CMD 
	- Using tools like **Streams** (Sysinternals)

## extended File Allocation Table (exFAT)

- File system optimized for **high-capacity USB flash drives and memory cards**.
- Maximum file size = **16EB**.
- Default file system for **SDXC cards**.
- Broader non-Windows OS support than NTFS — including **read and write on macOS**, recent version of Android, and Linux (but require some extra drivers to access exFAT)

## ext2, ext3 & ext4 (Linux File Systems)

- **1992:** The **Extended File System (ext)** was created for Linux — the **first native Linux file system.**
- **1993:** **ext2** was released.  
  - **Faster and more reliable** than the original ext  
  - Still used today for USB drives where journaling is not needed
- **2001:** **ext3** introduced **journaling**, which **helps prevent data corruption after crashes. 
- **2008:** **ext4** became the default file system for most Linux distributions.  
  - Faster, more efficient, and supports modern large-capacity drives

### Capacity Limits
- Supports **files up to 16TB**
- Supports **volumes up to 1EB (exabyte)**

### Compatibility
- **No native support in Windows or macOS**  
  (They require extra software to read or write ext2/3/4 partitions.)

### Summary
- **ext2** → No journaling, simple & fast  
- **ext3** → Adds journaling (safer)  
- **ext4** → Modern, fast, large-capacity, widely used today

## HFS, HFS+ & APFS (macOS File Systems)

- **HFS** stands for **Hierarchical File System** — the original file system for macOS.
- Also known as **"Mac OS Standard"**.
- **1998:** **HFS+** (also called **"HFS Extended"** or **"Mac OS Extended"**) was introduced.  
  - Added **journaling**, improving reliability and crash recovery.
- Supports **files and volumes up to 8EB** (as of macOS 10.4).
- **2017:** Apple launched **APFS (Apple File System)**.  
  - Optimized for SSDs  
  - Faster, more secure, supports snapshots and cloning
- **No native support** in **Windows** or **Linux** (requires third-party tools).

### Quick Summary
- **HFS** → Old Mac file system  
- **HFS+** → Modernized version with journaling  
- **APFS** → Current default macOS file system, optimized for SSDs and security


## ZFS (Zettabyte File System)

- **ZFS = Zettabyte File System** (often pronounced "zed file system").
- Originally created by **Sun Microsystems**; now maintained by the **OpenZFS Project**.
- Combines a **file system + volume manager** in one system.
- Offers **strong data protection**, including:
  - End-to-end checksums to detect and fix data corruption  
  - Snapshots and clones  
  - RAID-like features (RAID-Z)  
  - Self-healing of corrupted data
- Designed for **high reliability, scalability, and large storage systems**.
- Available on **Linux**, **FreeBSD**, **TrueOS**, and other UNIX-like systems (not native on macOS or Windows).

## Choosing a File System

- **System drives (OS installation):**
  - Use **NTFS** for Windows  
  - Use **ext4** for Linux  
  - Use **HFS+ / APFS** for macOS  

- **USB drives and memory cards:**
  - Use **FAT32** for capacities **below 32 GB**  
  - Use **exFAT** for **32 GB or larger** drives, or when storing **files larger than 4 GB**

- **External / general-purpose drives:**
  - Use **NTFS** if mainly used with Windows  
  - Use **exFAT** for best **PC + Mac compatibility**  
  - **FAT32** if you need compatibility with **older** devices
