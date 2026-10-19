
# 1️⃣ Conceptual Foundation

## Encryption Model Used by EFS (Encrypting File System)

EFS (a feature of **NTFS** in Microsoft Windows):

- Uses **symmetric encryption** to encrypt file contents (for performance).
- Uses **asymmetric encryption** to encrypt and protect the symmetric file key.
- The symmetric key of the file (FEK) **copy** is encrypted with both for **each copy**:
    - The user’s **EFS public key**
    - Any defined **Data Recovery Agent (DRA)** public key

⚠️ Critical rule:  
A DRA **must be configured before files are encrypted**, otherwise recovery is impossible.

# 2️⃣ Remove PC10 from the Domain (Required for Key Loss Simulation)

### Why?
EFS private key removal behavior in this lab works only on standalone systems (configuring DRA in AD is another story and require knowledge of AD)
### Steps:
1. Log in as **Jaime** (Domain Admin).
2. Open PowerShell as Administrator.
3. Run:
    
    ```powershell
    Remove-Computer -UnjoinDomaincredential Administrator -Restart -Force
    ```
1. Enter password.
2. System reboots.
3. Log in using **local Admin account**.
    
Result:  
PC10 becomes standalone (not domain-joined).

# 3️⃣ Create an EFS Data Recovery Agent (DRA)

## Step A – Generate DRA Certificate

1. Open Command Prompt as Admin.
2. Create folder:
    ```powershell
    mkdir c:\certificates
    cd c:\certificates
    ```
3. Create recovery certificate:
    ```powershell
    cipher /r:EFSRA
    ```
4. Set password (`Pa$$w0rd`)
- `EFSRA.CER` → Public key
- `EFSRA.PFX` → Password-protected private key

🔎 Important:
- CER = public recovery certificate
- PFX = private key (required for actual recovery)
## Step B – Add DRA to Local Security Policy

1. Run `mmc.exe`
2. Add **Group Policy Object Editor**
3. Navigate:
```
Computer Configuration
 → Windows Settings
   → Security Settings
     → Public Key Policies
       → Encrypting File System
```

1. Right-click → Add Data Recovery Agent
2. Import `EFSRA.CER`

Result:  
Admin is now defined as system DRA.

# 4️⃣ Create a Test User

Create local user:

```
net user pat Password1 /add
```

# 5️⃣ Encrypt Files with EFS

## Step A – Create Files (as Pat)
1. Log in as Pat.
2. Create folder:
    ```
    C:\SecReports
    ```
3. Create:
    - Jan-Security.txt
    - Feb-Security.txt
    - Mar-Security.txt
4. Add content to each file.
## Step B – Encrypt Using GUI

Right-click Jan-Security.txt → Properties → Advanced  
✔ Encrypt contents to secure data

Only encrypt the file (not folder).

## Step C – Encrypt via CLI

Check status:

```
cipher
```

Encrypt remaining:

```
cipher /e *-Security.txt
```

"E" = Encrypted  
"U" = Unencrypted

## Step D – Decrypt One File

```
cipher /d Mar-Security.txt
```

Result:
- Jan → Encrypted
- Feb → Encrypted
- Mar → Unencrypted

# 6️⃣ Access Control Behavior Demonstration

Log in as **Admin**.
- Cannot open Jan or Feb (encrypted by Pat).
- Can open Mar (not encrypted).

🔐 Key principle:  
EFS encryption overrides NTFS permissions if key is missing.

# 7️⃣ Break User’s EFS Access

## Step: Force Password Change

Log in as Admin:

```
net user Pat Password123
```

What happens internally:
- Windows discards Pat’s EFS private key.
- Pat loses ability to decrypt their files.

Log in as Pat:
- Cannot open Jan or Feb.
- Can open Mar.

Correct explanation:  
✔ Admin forcibly changed password  
❌ Not wrong computer  
❌ Not file ownership issue

# 8️⃣ DRA Recovery Process

Now demonstrate recovery capability.
## Step A – Attempt Decrypt Without Private Key

As Admin:

```
cipher /d Jan-Security.txt
```

Fails.

Check certificate info:

```
cipher /c Jan-Security.txt
```

Shows:  
Admin is recovery agent — but private key not installed.

## Step B – Import DRA Private Key

1. Navigate to:
    ```
    C:\certificates
    ```
2. Double-click:
    ```
    EFSRA.PFX
    ```
3. Enter password.
4. Complete import wizard.

Now Admin has DRA private key installed.

## Step C – Decrypt Using DRA

```
cipher /d Jan-Security.txt
```

Now successful.

Check:

```
cipher
```

Jan → U (Unencrypted)

---

## Step D – Decrypt via GUI

Right-click Feb-Security → Properties → Advanced  
Uncheck encryption.

All files now unencrypted.

# 9️⃣ Validate Recovery

Log in as Pat.
- All files accessible.
- Recovery was successful.

# 🔟 Key Technical Takeaways

## What a DRA Can Do

✔ Recover access to data encrypted with EFS  
❌ Cannot recover lost private key  
❌ Cannot restore deleted files  
❌ Cannot fix damaged storage

## Where EFS Can Be Used

✔ File Explorer  
✔ Command Prompt  
✔ PowerShell

❌ Z-shell (not native to Windows)

## Can DRA Be Defined After Encryption?

❌ No.  
Must exist **before encryption occurs**.

## What Is Inside EFSRA.PFX?

✔ The DRA private key  
❌ Not public key  
❌ Not symmetric key  
❌ Not disk encryption key

# 🔐 Security Architecture Summary

EFS Workflow:
1. File encrypted with symmetric key (FEK).
2. FEK encrypted with:
    - User’s public key
    - DRA public key (if configured)
3. Decryption requires matching private key.
4. Password reset destroys user’s EFS key.
5. DRA private key allows recovery.

# 🧠 Strategic Lessons

- Always configure DRA before enabling EFS in production.
- Always back up EFS private keys.
- Password resets in standalone systems destroy EFS access.
- DRA recovery depends entirely on protecting the PFX file.
