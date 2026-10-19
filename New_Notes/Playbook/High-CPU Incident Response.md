# Adaptive Lab: Using a Playbook

## Scenario

The SIEM detected abnormal CPU usage (~100%) on **PC10** (Windows Server 2019). The **SOAR system is offline** due to a failed reconfiguration, so a security professional must manually execute the High-CPU IR Playbook. A **Kali Linux VM** serves as the quarantine system.

**Environment:**

- **PC10** — Windows Server 2019 (victim/client machine)
- **KALI** — Kali Linux (quarantine system, IP: `10.1.16.66`)
- **User credentials:** `Jaime` / `Pa$$w0rd` and `root` / `Pa$$w0rd`

---

## Initial Setup — Launch the Rogue Process

From PowerShell as admin:
```powershell
C:\LABFILES\Playbook-Lab.ps1
```

|Element|Meaning|
|---|---|
|`C:\LABFILES\Playbook-Lab.ps1`|PowerShell script that launches the simulated rogue process|

- Sign into PC10 as `Jaime` using `Pa$$w0rd`
- Open PowerShell, run the script, then **close the PowerShell console immediately**
- The rogue process begins consuming nearly 100% CPU

> [!warning] Critical Step You **must close** the PowerShell console after launching the script. If left open, the rogue process becomes a **sub-process of PowerShell**, making it harder to identify and terminate correctly in later steps.

> [!note] Rogue Process Timeout The rogue process auto-terminates after **15 minutes**. Re-run the script if it is no longer consuming CPU when needed.

---

## Step 1 — Investigate High CPU Usage

**Goal:** Identify the name of the rogue process consuming most of the CPU.

### Option A — GUI: Task Manager

```
Taskbar Search → "task" → Task Manager → CPU column (sort descending)
```

- Switch to **Details view** if not already there (`More Details`)
- Sort by **CPU column** — the rogue process appears at the top
- If the top process is **Windows PowerShell**, the console was not closed after script launch — expand it to find the child process

### Option B — CLI: WMIC Utility

```cmd
wmic path Win32_PerfFormattedData_PerfProc_Process get Name,PercentProcessorTime
```

| Element                                    | Meaning                                                 |
| ------------------------------------------ | ------------------------------------------------------- |
| `wmic`                                     | Windows Management Instrumentation Command-line tool    |
| `Win32_PerfFormattedData_PerfProc_Process` | WMI class containing real-time process performance data |
| `get Name,PercentProcessorTime`            | Retrieves process name and its CPU usage percentage     |

- Output is **unsorted** — scroll through to find the highest `PercentProcessorTime`
- System has **2 virtual CPUs**, so total available CPU time = **200%**

### Option C — Third-Party GUI: Sysinternals Process Explorer

```
File Explorer → C:\SYSINTERNALS → Search "procexp" → Open procexp64
```

- Sort by **CPU column** descending
- The rogue process appears at the top with significant CPU usage
- Process name is shown **without** the `.exe` extension in this tool

> [!important] Key Concept — SOAR vs Playbook A **SOAR** (Security Orchestration, Automation, and Response) automatically scans threat intelligence from multiple sources and triggers corrective actions documented in **runbooks**. When the SOAR is offline, security staff use a **playbook** — a manual checklist of the same actions — to respond to the incident themselves.

---

## Step 2 — Terminate the Rogue Process

**Goal:** Kill the identified rogue process to stop the CPU consumption.

### Option A — GUI: Task Manager

```
Task Manager → Right-click <rogue process> → End Task
```

- Terminate **all instances** if multiple appear
- Close Task Manager after

### Option B — CLI: `taskkill` (Command Prompt)

```cmd
tasklist /FI "IMAGENAME eq <ProcessName>.exe
```

| Element        | Meaning                                        |
| -------------- | ---------------------------------------------- |
| `tasklist`     | Lists currently running processes              |
| `/FI`          | Filter — narrows results by specified criteria |
| `IMAGENAME eq` | Matches processes by executable filename       |

- Note the **PID** (Process ID) from the output

```cmd
taskkill /PID <PID> /F
```

| Element    | Meaning                                  |
| ---------- | ---------------------------------------- |
| `taskkill` | Terminates a running process             |
| `/PID`     | Specifies the process by its Process ID  |
| `/F`       | Force-terminates the process immediately |

**Verify termination:**

```cmd
tasklist /FI "IMAGENAME eq <ProcessName>.exe"
```

Expected result:

```
INFO: No tasks are running that match the specified criteria.
```

### Option C — CLI: `Stop-Process` (PowerShell)

```powershell
Get-Process <ProcessName>
```

| Element         | Meaning                                       |
| --------------- | --------------------------------------------- |
| `Get-Process`   | Retrieves information about running processes |
| `<ProcessName>` | Name of the target process (without `.exe`)   |

- Note the **Id** column (PID)

```powershell
Stop-Process -Id <PID> -Force
```

|Element|Meaning|
|---|---|
|`Stop-Process`|Terminates a process|
|`-Id`|Targets process by its numeric Process ID|
|`-Force`|Bypasses confirmation and forces termination|

**Verify termination:**

```powershell
Get-Process <ProcessName>
```

Expected result:

```
Get-Process : Cannot find a process with the name "<ProcessName>".
```

> [!note] Alternative PowerShell Method
> 
> ```powershell
> Stop-Process -Name <ProcessName> -Force
> ```
> 
> Terminates by name instead of PID — useful when PID is unknown.

### Option D — Third-Party CLI: Sysinternals `pskill`

```cmd
cd C:\sysinternals
pslist <ProcessName>
```

| Element  | Meaning                                                    |
| -------- | ---------------------------------------------------------- |
| `pslist` | Sysinternals tool that lists process details including PID |

- Note the **Pid** column

```cmd
pskill <PID>
```

| Element  | Meaning                                                    |
| -------- | ---------------------------------------------------------- |
| `pskill` | Sysinternals tool that terminates a process by PID or name |

**Verify termination:**

```cmd
pslist <ProcessName>
```

Expected result:

```
process <ProcessName> was not found
```

### Option E — Third-Party GUI: Sysinternals Process Explorer

```
C:/SYSINTERNALS → procexp64 (Process Explorer) → Right-click <rogue process> → Kill Process → OK
```

- Terminate all instances if multiples exist
- Close Process Explorer after

> [!important] Key Concept — Incident Response Playbooks Playbooks define **specific roles, processes, and procedures** for security staff to follow during an incident. They may exist as **physical books** in high-security environments to prevent digital exfiltration of IR capabilities and ensure availability during wide-scale incidents.

---

## Step 3 — Hash the Suspicious File

**Goal:** Locate the rogue process executable and generate a SHA256 hash for malware analysis.

### Locate the File First

```cmd
cd c:\ && dir /s <ProcessName>.exe
```

| Element  | Meaning                                                             |
| -------- | ------------------------------------------------------------------- |
| `cd c:\` | Changes working directory to root of C: drive                       |
| `&&`     | Runs the next command only after the first succeeds                 |
| `dir /s` | Lists files in current directory and all subdirectories recursively |

- Two results appear: `C:\Users\` and `C:\Program Files\JAM Software\HeavyLoad\`
- **Use only** `C:\Users\<ProcessName>.exe` for this lab
- The `C:\Program Files\` copy is the **original installation** — leave it intact so the lab can be reset in Step 8

> [!note] Command May Take Up to 1 Minute Searching the entire C: drive recursively can be slow. Press `CTRL+C` to terminate early once the path is confirmed.

### Option A — CMD: `certutil`

```cmd
cd c:\Users
certutil -hashfile "c:\Users\<ProcessName>.exe" SHA256 > c:\Users\<ProcessName>-Hash.txt
```

| Element                  | Meaning                                             |
| ------------------------ | --------------------------------------------------- |
| `certutil`               | Windows certificate utility — also used for hashing |
| `-hashfile`              | Calculates the hash of the specified file           |
| `SHA256`                 | Hashing algorithm used                              |
| `>`                      | Redirects output to a file instead of the console   |
| `<ProcessName>-Hash.txt` | Output file storing the hash value                  |

**Display the hash:**

```cmd
type <ProcessName>-Hash.txt
```

### Option B — PowerShell: `Get-FileHash`

```powershell
cd C:\Users
Get-FileHash -Path "c:\Users\<ProcessName>.exe" -Algorithm SHA256 > c:\Users\<ProcessName>-Hash.txt
```

| Element             | Meaning                                                   |
| ------------------- | --------------------------------------------------------- |
| `Get-FileHash`      | PowerShell cmdlet that computes cryptographic file hashes |
| `-Path`             | Specifies the file to hash                                |
| `-Algorithm SHA256` | Selects the SHA256 hashing algorithm                      |

**Display the hash:**

```powershell
type <ProcessName>-Hash.txt
```

### Option C — Third-Party CLI: Sysinternals `sigcheck`

```cmd
C:\sysinternals\sigcheck.exe -h "c:\Users\<ProcessName>.exe" > c:\Users\<ProcessName>-Hash.txt
```

| Element        | Meaning                                                                                     |
| -------------- | ------------------------------------------------------------------------------------------- |
| `sigcheck.exe` | Sysinternals tool that verifies file signatures and computes multiple hashes simultaneously |
| `-h`           | Displays hash values (MD5, SHA1, SHA256) for the file                                       |

**Display the hash:**

```cmd
type <ProcessName>-Hash.txt
```


> [!important] Key Concept — Why SHA256? Cryptographic hashes create a **unique digital fingerprint** of a file. SHA256 is used for malware analysis because even a **single bit change** in the file produces a completely different hash, making it impossible to tamper with the file without detection. The hash is submitted to online services to check against known malware databases.

## Step 4 — Online Malware Analysis

**Goal:** Submit the file's SHA256 hash to an online malware scanning service to determine if it is known malware.

**Hash used in this lab:**

```
705204f80158bfbf9216a19bc2cb25a392ff028ce028745ac16f543b6290ec81
```

### Option A — Hybrid Analysis

```
https://www.hybrid-analysis.com/ → Report Search → paste hash → Search
```

### Option B — MetaDefender (OPSWAT)

```
https://metadefender.opswat.com/ → "Trust no" field → paste hash → Process
```

### Option C — VirusTotal

```
https://www.virustotal.com/ → Search tab → paste hash → Enter
```

**Result for all three services:**

```
Hash is unknown / not identified as malware
```

> [!important] Key Concept — Indeterminate Results An unknown hash means the file is **either benign or a new, unclassified malicious file**. Because the result is inconclusive, the playbook directs you to **continue with all remaining steps** — treat it as suspicious until proven safe. This reflects real SOC decision-making where unknown ≠ safe.

> [!note] Testing with a Known Malicious Hash To see a positive malware detection result, search this hash on Hybrid Analysis:
> 
> ```
> 82407eaf6437d6956f63e85b28c0ec6ca58d298a
> ```
> 
> This is the hash of `ca_setup.exe` — the installer for **Cain**, a known password cracking and network attack tool.

> [!important] Key Concept — Runbooks vs Playbooks When a playbook is **heavily automated by a SOAR**, it becomes a **runbook**. Runbooks automate routine steps while preserving **human decision points** — moments where an analyst reviews contextual data (like a malware scan result) and decides how to proceed. A SOAR could auto-submit hashes to VirusTotal and act on results without human input.

---

## Step 5 — Determine the Owner of the Suspicious File

**Goal:** Identify which user account owns the suspicious executable.

### Option A — GUI: File Explorer

```
File Explorer → C:\ → Users → Right-click <ProcessName> → Properties → Security tab → Advanced
```

- The **Owner** field displays the account that owns the file
- Do **not** include parenthetical information in your answer (e.g., if shown `PC10\Jaime (Jaime)`, enter only `Jaime`)

> [!note] File Explorer hides known file extensions by default — `HeavyLoad.exe` appears as just `HeavyLoad`.

### Option B — CMD: `dir /q`

```cmd
dir /q c:\Users\HeavyLoad.exe
```

|Element|Meaning|
|---|---|
|`dir`|Lists directory contents|
|`/q`|Displays the **owner** of each file alongside its details|

- Exclude the system name prefix from your answer (e.g., from `PC10\Jaime`, enter only `Jaime`)

### Option C — PowerShell: `Get-Acl`

```powershell
(Get-Acl -Path "C:\Users\<ProcessName>.exe").Owner
```

| Element   | Meaning                                                     |
| --------- | ----------------------------------------------------------- |
| `Get-Acl` | Retrieves the Access Control List (ACL) of a file or object |
| `-Path`   | Specifies the file to inspect                               |
| `.Owner`  | Extracts only the Owner property from the ACL object        |

- Exclude the system name prefix from your answer

> [!important] Key Concept — Why Identify the Owner? Determining file ownership helps establish **who placed the suspicious file on the system** — a key part of incident attribution. It helps identify whether the file was placed by a **legitimate user**, a **compromised account**, or an **unauthorized actor**, which feeds directly into the incident report (Step 9).

---

## Step 6 — Archive the Suspicious File

**Goal:** Package the suspicious executable and its hash file together into a `.zip` archive for transfer to quarantine.

### Option A — GUI: File Explorer

```
C:\Users → Select <ProcessName> + <ProcessName>-Hash (Shift+click both) → Right-click → Send to → Compressed (zipped) folder → Yes
```

- File Explorer **cannot create files directly in** `C:\Users` — it saves the zip to the **Desktop** instead
- Move the zip from Desktop to `C:\` using drag-and-drop
- Select **Continue** on the "Destination Folder Access Denied" prompt

> [!warning] Right-click over `<ProcessName>` (not `<ProcessName>-Hash`) when both are selected — otherwise the archive will be named `<ProcessName>-Hash.zip` instead of `<ProcessName>.zip`.

### Option B — CMD: `tar`

```cmd
tar -c -a -f "C:\<ProcessName>.zip" "C:\Users\<ProcessName>.exe" "C:\Users\<ProcessName>-Hash.txt"
```

|Element|Meaning|
|---|---|
|`tar`|Archive utility (available natively in Windows 10+)|
|`-c`|Create a new archive|
|`-a`|Auto-detect archive format from file extension (`.zip`)|
|`-f`|Specifies the output archive filename|

Expected message:

```
tar: Removing leading drive letter from member names
```

**Verify archive was created:**

```cmd
dir c:\
```

### Option C — PowerShell: `Compress-Archive`

```powershell
Compress-Archive -Path "C:\Users\<ProcessName>.exe", "C:\Users\<ProcessName>-Hash.txt" -DestinationPath "C:\<ProcessName>.zip"
```

|Element|Meaning|
|---|---|
|`Compress-Archive`|PowerShell cmdlet that creates `.zip` archives|
|`-Path`|Comma-separated list of files to include in the archive|
|`-DestinationPath`|Output path and filename for the resulting `.zip` file|

**Verify archive was created:**

```powershell
dir c:\
```

> [!note] Avoiding Overwrite Conflicts If you run multiple archiving methods, delete the existing zip first to avoid overwrite prompts:
> 
> ```cmd
> del c:\<ProcessName>.zip
> ```

---

## Step 7 — Copy Archive to Quarantine System (Kali)

**Goal:** Transfer the archived suspicious file to the Kali Linux quarantine system at `10.1.16.66`.

### Option A — Netcat + PowerShell

**On Kali:**

```bash
mkdir quarantine
cd quarantine
nc -l -p 1234 > HeavyLoad.zip
```

| Element           | Meaning                                              |
| ----------------- | ---------------------------------------------------- |
| `nc`              | Netcat — network utility for raw TCP/UDP connections |
| `-l`              | Listen mode — waits for an incoming connection       |
| `-p 1234`         | Listens on port 1234                                 |
| `> HeavyLoad.zip` | Saves all received data into this file               |

**On PC10 (PowerShell):**

```powershell
$filePath = "C:\HeavyLoad.zip"; $destination = "10.1.16.66"; $port = 1234; $tcpConnection = New-Object System.Net.Sockets.TcpClient($destination, $port); $netStream = $tcpConnection.GetStream(); $buffer = [System.IO.File]::ReadAllBytes($filePath); $netStream.Write($buffer, 0, $buffer.Length); $netStream.Close(); $tcpConnection.Close(); $?;
```

| Element                        | Meaning                                                         |
| ------------------------------ | --------------------------------------------------------------- |
| `System.Net.Sockets.TcpClient` | .NET class that establishes a TCP connection to the destination |
| `GetStream()`                  | Opens a network stream for writing data                         |
| `ReadAllBytes($filePath)`      | Reads the entire zip file into memory as a byte array           |
| `$netStream.Write(...)`        | Sends the byte array across the TCP connection                  |
| `$?`                           | Returns `True` if the last command succeeded                    |

- Press **Enter** one final time to execute the trailing `$?` line
- This replicates Netcat's file transfer functionality **entirely within PowerShell** — no Netcat needed on Windows

**Verify on Kali:**

```bash
ls -l
unzip -t HeavyLoad.zip
```

Expected result:

```
No errors detected in compressed data of HeavyLoad.zip
```

### Option B — WinSCP (GUI)

```
Search → "winscp" → Open WinSCP
```

**Login settings:**

|Field|Value|
|---|---|
|File protocol|SCP|
|Host name|`10.1.16.66`|
|Port|`22`|
|Username|`root`|
|Password|`Pa$$w0rd`|

- Select **Yes** on the host key warning (first-time connection)
- Create a `quarantine` folder on Kali (right pane → New → Directory)
- Navigate to `C:\` in the left pane
- Select `HeavyLoad.zip` → Upload → OK

**Verify on Kali:**

```bash
cd /root/quarantine
ls -l
unzip -t HeavyLoad.zip
```

### Option C — Samba Share

**On Kali — Configure Samba:**

```bash
mkdir quarantine
cd quarantine
nano /etc/samba/smb.conf
```

Navigate to end of file (`CTRL+/` → type `500` → Enter), then append:

```ini
[quarantine]
  path = /root/quarantine
  browsable = yes
  read only = no
  guest ok = no
  valid users = root
```

```bash
smbpasswd -a root        # Set Samba password for root (use Pa$$w0rd)
systemctl restart smbd   # Apply new Samba configuration
```

|Element|Meaning|
|---|---|
|`smbpasswd -a`|Adds a Samba user and sets their password|
|`systemctl restart smbd`|Restarts the Samba daemon to load new config|

**On PC10 (CMD):**

```cmd
net use z: \\10.1.16.66\quarantine /user:root
```

|Element|Meaning|
|---|---|
|`net use`|Maps a network share to a local drive letter|
|`z:`|Drive letter assigned to the network share|
|`\\10.1.16.66\quarantine`|UNC path to the Samba share on Kali|
|`/user:root`|Authenticates as the root user|

```cmd
cd C:\
copy HeavyLoad.zip z:\HeavyLoad.zip
net use z: /delete
```

|Element|Meaning|
|---|---|
|`copy`|Copies a file to the specified destination|
|`net use z: /delete`|Removes the mapped network drive|

Expected result:

```
1 file(s) copied.
```

**Verify on Kali:**

```bash
cd /root/quarantine
ls -l
unzip -t HeavyLoad.zip
```

> [!important] Key Concept — Why Quarantine? Copying the suspicious file to an **isolated quarantine system** preserves evidence for forensic analysis while preventing the file from executing or spreading further. The archive contains both the executable and its hash file, enabling later verification that the file was not tampered with during transfer.

---

## Step 8 — Remove the Suspicious File from the Victim

**Goal:** Delete the suspicious file and associated files from PC10 to remediate the affected system.

### Option A — GUI: File Explorer + Recycle Bin

```
C:\ → Right-click HeavyLoad.zip → Delete
C:\Users → Right-click HeavyLoad → Delete
C:\Users → Right-click HeavyLoad-Hash → Delete
Desktop → Double-click Recycle Bin → Recycle Bin Tools → Empty Recycle Bin → Yes
```

> [!warning] Not Secure Deletion File Explorer deletion sends files to the **Recycle Bin** first. Even after emptying the Recycle Bin, the data blocks are only marked as reusable — **not overwritten**. A third-party undelete tool may still recover the file contents.

### Option B — CMD: `del`

```cmd
del c:\HeavyLoad.zip
dir
del c:\Users\HeavyLoad*
dir c:\Users
```

| Element      | Meaning                                                                    |
| ------------ | -------------------------------------------------------------------------- |
| `del`        | Deletes specified file(s) directly (bypasses Recycle Bin)                  |
| `HeavyLoad*` | Wildcard — matches `HeavyLoad.exe` and `HeavyLoad-Hash.txt` simultaneously |

> [!warning] Not Secure Deletion `del` bypasses the Recycle Bin but **does not overwrite** the data. File contents may still be recoverable with forensic tools.

### Option C — Third-Party CLI: Sysinternals `sdelete`

```cmd
sdelete -p 3 HeavyLoad.exe
dir
sdelete -p 3 c:\Users\HeavyLoad*
dir c:\Users
```

|Element|Meaning|
|---|---|
|`sdelete`|Sysinternals secure deletion tool|
|`-p 3`|Overwrites the file's storage sectors **3 times** with random data before deletion|

- After overwriting, `sdelete` also **zeroes the directory entry** to prevent metadata recovery
- This is the **only method in this step that performs secure deletion**

|Method|Bypasses Recycle Bin|Overwrites Data|Secure|
|---|---|---|---|
|File Explorer + Empty Recycle Bin|✅ After emptying|❌ No|❌ No|
|`del`|✅ Yes|❌ No|❌ No|
|`sdelete -p 3`|✅ Yes|✅ Yes (3 passes)|✅ Yes|

---

## Step 9 — Craft an Incident Report (AAR)

**Goal:** Document all response activities and submit to the CISO/SOC for review.

The report is known as an **After Action Report (AAR)** — also called a **Lessons Learned** or **Post-Mortem** report.

**The AAR should include:**

- Summary of each playbook step performed
- Method chosen for each step and why
- Results obtained at each step
- Problems or discrepancies encountered
- Recommendations for improving the playbook, toolset, or environment

> [!important] Key Concept — Purpose of the AAR: The AAR is not just documentation — it is a **feedback mechanism** that drives continuous improvement of the organization's incident response capability. It may reveal gaps in playbook coverage, missing tools, unclear instructions, or environmental issues that hindered the response.

---

## Comprehensive Questions

|Question|Answer|
|---|---|
|Which playbook task must occur before all others?|**Determine the rogue process's name** — you cannot terminate, hash, or analyze a process you haven't identified|
|What asset defines the steps, roles, and procedures for responding to an incident?|**Playbook**|
|A playbook is what type of security control?|**Responsive** — directs corrective actions after an incident is confirmed|
|What security solution scans threat intelligence from multiple sources and may trigger automated responses?|**SOAR**|
|Which statements about playbooks are true?|A playbook is used by a person; a runbook is used by a SOAR. The most effective playbooks are tailored to an organization's specific needs. All relevant stakeholders should be involved in playbook creation.|

---

## Key Concepts Summary

| Term         | Definition                                                                                                                                                   |
| ------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **Playbook** | A manual checklist of actions for responding to a specific incident type — a **responsive control**                                                          |
| **Runbook**  | An automated version of a playbook executed by a SOAR system with defined human decision points                                                              |
| **SOAR**     | Security Orchestration, Automation, and Response — automates threat detection and response using threat Cyber Intelligence Feeds (CTI) feeds, UEBA, and APIs |
| **SIEM**     | Security Information and Event Management — collects and analyzes log data; detected the abnormal CPU spike in this scenario                                 |
| **AAR**      | After Action Report — post-incident documentation used to improve future response processes                                                                  |
| **SOC**      | Security Operations Center — the team that receives and reviews incident reports                                                                             |
