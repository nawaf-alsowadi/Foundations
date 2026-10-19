Detect when something is in undesirable/less secure state then try to move it to the /desirable/more secure state. Sometimes, it can repair (fix) minor damage to restore to more secure state and may prevent its reoccurrence. Operates after the attck

## Sysinternals
A website that offer tools to diagnose and troubleshoot Windows.

**Notmyfault** is one of the tools that let windows experience a system crash because of misbehaving actions to test if Windows system will trigger its in-built native corrective control protection to protect the system and restore it to its normal state.
![[Pasted image 20260227185210.png]]

Whem we select **crash**, the system will immeditly will shot **BSOD (Blue Screen Of Death)**![[Pasted image 20260227185423.png]]
and perform memory dump for potential analysis and then reboot and restore the system  to its secure state.


# PowerShell Corrective Control – File Integrity Monitoring

## Objective
Implement a **corrective control** that:
1. Detects changes in a file using hashing.
2. Restores the file to its approved content if modified.
3. Automates the process using PowerShell scripts.

---

## Step 1: Create the Protected File
```powershell
"This is important" | Set-Content notes.txt
type notes.txt
````

Creates `notes.txt` with approved content.

---

## Step 2: Generate and Store File Hash (Integrity Baseline)

```powershell
Get-FileHash ./notes.txt -Algorithm SHA256 | Select-Object -ExpandProperty Hash | Set-Content ./hash.txt
type hash.txt
```

- Uses **SHA256 hashing**
- Stores baseline hash in `hash.txt`
---

## Step 3: Simulate File Tampering

```powershell
echo blah >> notes.txt
type notes.txt
```

`>>` appends content, modifying the file.

---

## Step 4: Manual Integrity Check

```powershell
if((Get-FileHash ./notes.txt -Algorithm SHA256).Hash -eq (Get-Content ./hash.txt)) {
    Write-Host "The file is correct."
} else {
    Write-Host "The file has changed. Corrective action should be initiated."
}
```

- Compares current hash with stored baseline.
- Detects modification.
---

## Step 5: Manual Corrective Action

```powershell
"This is important" | Set-Content notes.txt
```

Restores approved content.

---

# Automation

## Script 1: Calculate Hash (`calchash.ps1`)

```powershell
Get-FileHash ./notes.txt -Algorithm SHA256 |
Select-Object -ExpandProperty Hash |
Set-Content ./hash.txt
```

Run:

```powershell
./calchash.ps1
```

Purpose:

- Recreates baseline hash after approved changes.
---

## Script 2: Check and Auto-Correct (`check.ps1`)

```powershell
if((Get-FileHash ./notes.txt -Algorithm SHA256).Hash -ne (Get-Content ./hash.txt))
{
  "This is important" | Set-Content ./notes.txt
  Write-Host "The file has changed. Corrective action initiated."
}
else
{
  Write-Host "The file is correct. No corrective action needed."
}
```

Run:

```powershell
./check.ps1
```

Behavior:
- If hash mismatch → restores file.
- If match → no action.

---

## Real-World Parallel

Similar to **Windows Signature Verification (SigVerif)**:

- Validates system files before boot.
- Replaces corrupted files with valid versions.
- Ensures system integrity.
# Deterrent Security Control
Display a warning sign or Acceptable Use Policy (AUP) to psychologically discourage attack.

# Compensation Security Control
Compensate (substitute) a failed control. Associated with framework compliance measures For example, taking a backup so when a preventive control fail when can restore the damaged thing.

