
# 1. Set File Permissions on Linux

## Objective

Correct permissions of an important file **demofile.sh** so that:

|User Type|Required Permission|
|---|---|
|Owner|Full access (rwx)|
|Group|Execute only (x)|
|Others|No access|

Final permissions should be:

```
-rwx--x---
```

Octal value:

```
710
```

# Step 1 — Open Terminal and Check Location

### Command

```bash
pwd
```

### Explanation

|Element|Meaning|
|---|---|
|`pwd`|Print Working Directory|

Expected output:

```
/root
```

If not in root's home directory:

```bash
cd ~
```

|Element|Meaning|
|---|---|
|`cd`|Change directory|
|`~`|Current user home directory|

Root's home directory:

```
/root
```

# Step 2 — List Files and Permissions

### Command

```bash
ls -l
```

### Explanation

|Element|Meaning|
|---|---|
|`ls`|List directory contents|
|`-l`|Long list format (shows permissions, owner, size, date)|

Example output:

```
-rwxrwxrwx 1 root root 512 testfile.txt
```

Permission structure:

```
-rwxrwxrwx
```

Breakdown:

|Position|Meaning|
|---|---|

- | File type (- for file, d for directory) |  
    rwx | Owner permissions |  
    rwx | Group permissions |  
    rwx | Others permissions |
    

### Filter Specific Files

```bash
ls -l te*
```

Explanation:

|Element|Meaning|
|---|---|
|`te*`|Wildcard matching files starting with "te"|

# Step 3 — Modify Permissions Using Symbolic Mode

Linux permissions use **symbolic representation**.

Symbols:

|Symbol|Meaning|
|---|---|
|u|user/owner|
|g|group|
|o|others|

### Add Execute Permission to Owner

```bash
chmod u+x testfile.txt
```

Explanation:

|Part|Meaning|
|---|---|
|`chmod`|Change file mode (permissions)|
|`u+x`|Add execute permission to owner|

### Add Write Permission to Group

```bash
chmod g+w testfile.txt
```

Meaning:

```
Group gains write permission
```

### Remove Multiple Permissions

```bash
chmod go-r,u-x testfile.txt
```

Explanation:

| Part   | Meaning                           |
| ------ | --------------------------------- |
| `go-r` | Remove read from group and others |
| `u-x`  | Remove execute from owner         |

# Step 4 — Use Octal Permissions

Linux permissions can also be set using **octal numbers**.

Each permission has a value:

|Permission|Value|
|---|---|
|Read|4|
|Write|2|
|Execute|1|

Total values:

| Number | Permission |
| ------ | ---------- |
| 7      | rwx        |
| 6      | rw         |
| 5      | rx         |
| 4      | r          |
| 3      | wx         |
| 2      | w          |
| 1      | x          |
| 0      | none       |

### Example Commands

#### Full access for everyone

```bash
chmod 777 testfile.txt
```

Result:

```
rwxrwxrwx
```

#### Owner full, group read, others none

```bash
chmod 740 testfile.txt
```

Result:

```
rwxr-----
```

#### Mixed permissions

```bash
chmod 654 testfile.txt
```

Result:

```
rw-r-xr--
```

---
# 2. Manage NTFS Permissions (Windows)

## Objective

View and modify permissions of a **Windows** file.

File:

```
comptia-logo.jpg
```

# Step 1 — Open PowerShell and Navigate

```powershell
cd C:\LABFILES
```

# Step 2 — View File Permissions

```powershell
icacls .\comptia-logo.jpg
```

Explanation:

| Element  | Meaning                                                                                          |
| -------- | ------------------------------------------------------------------------------------------------ |
| `icacls` | **Integrity Control Access Control Lists**, Windows command for viewing/editing NTFS permissions |
| `.\`     | current directory                                                                                |

|Part|Meaning|
|---|---|
|**I**|Integrity (related to Windows Mandatory Integrity Control)|
|**C**|Control|
|**ACL**|Access Control List|
|**S**|plural (lists)|

Users group permission:

```
ReadAndExecute
```

# Step 3 — Deny Permission

```powershell
icacls .\comptia-logo.jpg /deny dylan:R
```

Explanation:

|Element|Meaning|
|---|---|
|`/deny`|explicitly deny permission|
|`dylan`|user account|
|`R`|read permission|

# Step 4 — Grant Permission

```powershell
icacls .\comptia-logo.jpg /grant dylan:F
```

Explanation:

|Element|Meaning|
|---|---|
|`/grant`|give permission|
|`F`|Full Control|

# Step 5 — Remove Explicit Permissions

```powershell
icacls .\comptia-logo.jpg /remove:g dylan
```

Explanation:

|Element|Meaning|
|---|---|
|`/remove`|remove permission entry|
|`g`|grant permission entry|

Important:

Removing direct permissions **does not remove inherited permissions** from groups.

---
# 3. Review Effective Permissions

## Objective

Determine **what access a user actually has**.

Effective access considers:

| Factor                                         |
| ---------------------------------------------- |
| User                                           |
| Group membership                               |
| Share permissions                              |
| Currently used System / workstation / Computer |

### Steps

1. Open **File Explorer**
2. Navigate to

```
C:\LABFILES
```

3. Right-click file → **Properties**
4. Open **Security tab**
5. Click **Advanced**
6. Open **Effective Access**

### Select User

Enter:

```
dylan
```

Click:

```
View effective access
```

---
# 4. Manage Windows Share Permissions

## Objective

Create and manage SMB network share permissions.

# Step 1 — Create Share

```powershell
New-SmbShare -Name "LABFILES" -Path "C:\LABFILES" -Description "Share for LABFILES"
```

Explanation:

| Parameter      | Meaning          |
| -------------- | ---------------- |
| `New-SmbShare` | create SMB share |
| `-Name`        | share name       |
| `-Path`        | folder path      |
| `-Description` | optional comment |

# Step 2 — View Shares

```powershell
Get-SmbShare
```

Example existing share:

```
ADMIN$
```

Explanation:

|Share|Purpose|
|---|---|
|ADMIN$|administrative remote management share|

# Step 3 — View Share Permissions

```powershell
Get-SmbShareAccess -Name "LABFILES"
```

Shows which users/groups can access the share.

# Step 4 — Grant Share Permission

```powershell
Grant-SmbShareAccess -Name "LABFILES" -AccountName "dylan" -AccessRight Change
```

Explanation:

|Parameter|Meaning|
|---|---|
|`Grant-SmbShareAccess`|grant share permission|
|`AccountName`|user or group|
|`AccessRight`|permission level|

Share permission levels:

|Permission|Meaning|
|---|---|
|Read|view files|
|Change|read + write + delete|
|Full Control|full administrative access|

# Step 5 — Remove Share Permission

```powershell
Revoke-SmbShareAccess -Name "LABFILES" -AccountName "dylan"
```

Explanation:

|Parameter|Meaning|
|---|---|
|`Revoke-SmbShareAccess`|remove share permission|

# Important Security Rule

When accessing files over network:

```
Effective Permission =
Most restrictive of NTFS and Share permissions
```

Example:

|NTFS|Share|Result|
|---|---|---|
|Modify|Read|Read|

# Key Exam Answers


### NTFS permission giving complete control

```
Full Control
```

### Effective permissions are calculated from

✔ Share permissions  
✔ Group membership  
✔ User  
✔ System / Workstation / Computer

### Required share permission to modify file

```
Change
```

### Windows tool for viewing/editing permissions

```
icacls
```
