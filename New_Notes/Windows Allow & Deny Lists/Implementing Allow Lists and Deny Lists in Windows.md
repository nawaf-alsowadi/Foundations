# Assisted Lab: Implementing Allow Lists and Deny Lists — Lab Summary

## Step 1 — Enable AppLocker and Start Required Services

### Start the Application Identity Service

```powershell
net start appidsvc
```

| Element     | Meaning                                                                                                                     |
| ----------- | --------------------------------------------------------------------------------------------------------------------------- |
| `net start` | Starts a Windows service                                                                                                    |
| `appidsvc`  | Application Identity Service — **required for AppLocker to function**; identifies and verifies the identity of applications |

> [!important] Key Concept: AppLocker **will not enforce rules** unless the Application Identity Service (`appidsvc`) is running. This is the most commonly overlooked step when AppLocker rules fail to apply.

### Open Local Security Policy

```
Taskbar Search → "secpol.msc" → Local Security Policy
```

Navigate to:

```
Application Control Policies → AppLocker → AppLocker
```

In the right pane → **Configure rule enforcement** → Mark **all 4 Configured checkboxes** → Set all 4 dropdowns to **Enforce rules** → Apply → OK

|Rule Category|Purpose|
|---|---|
|Executable Rules|Controls `.exe` and `.com` files|
|Windows Installer Rules|Controls `.msi` and `.msp` files|
|Script Rules|Controls `.ps1`, `.bat`, `.cmd`, `.vbs`, `.js` files|
|Packaged App Rules|Controls Microsoft Store apps|

---

## Step 2 — Create a Path-Based Deny Rule (Registry Editor)

**Goal:** Block `regedit.exe` from executing based on its file path.

### Navigation

```
Local Security Policy → Executable Rules → Right-click → Create New Rule
```

**Wizard settings:**

|Page|Setting|
|---|---|
|Permissions → Action|**Deny**|
|Permissions → User or group|**Everyone**|
|Conditions|**Path**|
|Path value|`%WINDIR%\regedit.exe`|

| Element       | Meaning                                                                                      |
| ------------- | -------------------------------------------------------------------------------------------- |
| `%WINDIR%`    | Environment variable pointing to the Windows installation directory (typically `C:\Windows`) |
| `regedit.exe` | Target executable to block                                                                   |

- If prompted to create default rules → Select **Yes**

### Apply the Policy

```powershell
gpupdate /force
```

|Element|Meaning|
|---|---|
|`gpupdate`|Forces a Group Policy refresh on the local machine|
|`/force`|Reapplies all policies regardless of whether they have changed|

Wait for both **Computer Policy** and **User Policy** update confirmation messages.

### Test the Rule — CLI

```powershell
regedit
```

Expected result:

```
The program failed to run because it is blocked by group policy.
```

### Test the Rule — GUI

```
Taskbar Search → "regedit" → Registry Editor
```

Expected result:

```
This app has been blocked by your system administrator.
```

> [!important] Key Concept — Path Rule Limitation: A **Path rule** blocks execution based solely on the **file name and location**. If the executable is **renamed or moved** to a different directory, the rule is bypassed and the application will execute successfully. Path rules are easy to configure but offer **weaker protection** than hash rules.

---

## Step 3 — Create a Hash-Based Deny Rule (Firefox)

**Goal:** Block Firefox from executing based on its file hash — regardless of filename or location.

### Navigation

```
Local Security Policy → Executable Rules → Right-click → Create New Rule
```

**Wizard settings:**

|Page|Setting|
|---|---|
|Permissions → Action|**Deny**|
|Permissions → User or group|**Everyone**|
|Conditions|**File hash**|
|File Hash → Browse Files|`C:\Program Files\Mozilla Firefox\firefox.exe`|

- Navigate: **This PC → Local Disk (C:) → Program Files → Mozilla Firefox → select firefox → Open**
- Select **Create** to finalize the rule
- Result: `firefox.exe | File Hash Deny` rule appears in Executable Rules

### Apply the Policy

```powershell
gpupdate /force
```

### Test the Rule

```
Taskbar Search → "Firefox" → Firefox
```

Expected result:

```
This app has been blocked by your system administrator.
```

> [!important] Key Concept — Hash Rule vs Path Rule
> 
> |Rule Type|Blocked if Renamed?|Blocked if Moved?|Overhead|
> |---|---|---|---|
> |**Path**|❌ No|❌ No|Low|
> |**File Hash**|✅ Yes|✅ Yes|Higher|
> 
> A **Hash rule** computes a cryptographic fingerprint of the executable. Since the hash is tied to the **file's contents** — not its name or location — renaming or moving the file **does not bypass the rule**. Hash rules are more restrictive but require more system overhead and must be **updated whenever the application is patched or updated** (since the hash changes).

---

## AppLocker Rule Conditions — Overview

|Condition|Based On|Bypass by Rename?|Bypass by Moving?|
|---|---|---|---|
|**Publisher**|Digital signature / certificate|❌ No|❌ No|
|**File Hash**|Cryptographic hash of file contents|❌ No|❌ No|
|**Path**|File name and directory location|✅ Yes|✅ Yes|

---

## Comprehensive Questions

|Question|Answer|
|---|---|
|What AppLocker rule type can be bypassed by renaming or moving the executable?|**Path**|
|What is the general term for the function AppLocker performs in this lab?|**Block listing** (deny listing)|
|What native Windows mechanisms can block a user's ability to execute an application?|**NTFS permissions** and **AppLocker**|
|What type of security control denies execution unless the process is explicitly authorized?|**Allow list** (approved list)|
|What is the correct definition of a block list (deny list)?|Generally allows execution but **explicitly prohibits listed processes**|

---

## Key Concepts Summary

|Term|Definition|
|---|---|
|**AppLocker**|Native Windows feature for defining application execution allow/deny policies via Group Policy|
|**Allow List**|Denies all execution by default — only explicitly approved applications may run|
|**Block List / Deny List**|Allows execution by default — explicitly listed applications are denied|
|**appidsvc**|Application Identity Service — must be running for AppLocker rules to be enforced|
|**gpupdate /force**|Forces immediate Group Policy refresh so new AppLocker rules take effect without rebooting|
|**Path Rule**|Blocks based on file location and name — can be bypassed by renaming or moving the file|
|**Hash Rule**|Blocks based on cryptographic file fingerprint — cannot be bypassed by renaming or moving|
|**Principle of Least Privilege**|Security principle of restricting users to only the applications and permissions needed for their role|