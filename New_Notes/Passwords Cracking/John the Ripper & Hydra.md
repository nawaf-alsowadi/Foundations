Below is a **clear structured summary of the entire lab with the commands and explanations**, organized by the four major exercises.

---

# 1. Password Spraying Attack

## 🎯 Goal

Identify **which user accounts use a known password** by trying a **small list of passwords across many accounts**.

Password spraying is designed to **avoid account lockout** by:
- trying **1–2 passwords per account**
- moving to the next user.
  
# Step 1 — Create Mount Point

```sh
mkdir /mnt/HR
```

### Explanation

- `mkdir` → create directory
- `/mnt/HR` → local folder used to mount the remote share

This will be used if valid credentials are discovered.

# Step 2 — View Known User Accounts

```sh
cat users.txt
```

Example accounts:

```sh
jaime
alex
jordan
logan
pat
```

# Step 3 — Manual Password Testing

Try mounting the Windows share.

```sh
mount //10.1.16.2/HR /mnt/HR -o username=pat
```

### Command breakdown

|Component|Meaning|
|---|---|
|`mount`|attach remote filesystem|
|`//10.1.16.2/HR`|SMB share|
|`/mnt/HR`|local mount point|
|`-o`|mount options|
|`username=pat`|login user|

Enter password:

```sh
abc123
```

Result → **Mount error**

Repeat:

```sh
mount //10.1.16.2/HR /mnt/HR -o username=pat
```

Password:

```sh
123456
```

Result → **Mount error**

Manual testing is slow → automation required.

# Step 4 — Create Password List

Create password file:

```sh
echo abc123 > pass.txt
```


Add more passwords:

```sh
echo 123456 >> pass.txt
echo 'Pa$$w0rd' >> pass.txt
```

Explanation:

| Symbol | Meaning                        |
| ------ | ------------------------------ |
| `>>`   | append to file                 |
| `' '`  | prevent shell interpreting `$` |

Verify:

```sh
cat pass.txt
```

Output:

```sh
abc123
123456
Pa$$w0rd
```

# Step 5 — Run Password Spraying with Hydra

Start Hydra wizard:
```
hydra-wizard
```

Provide inputs:

|Prompt|Value|
|---|---|
|Service|smb|
|Target|10.1.16.2|
|Username|users.txt|
|Password|pass.txt|
|Run command|Y|

## How Hydra Works

Hydra attempts:

```sh
user1 + pass1
user1 + pass2
user1 + pass3
user2 + pass1
user2 + pass2
```

Attack type:

**Password spraying**

## Result

Hydra discovered password:

```sh
Pa$$w0rd
```

Used by:

```sh
Jaime
Alex
```


# Step 6 — Verify Credentials

Mount share:

```sh
mount //10.1.16.2/HR /mnt/HR -o username=jaime
```

Password:

```sh
Pa$$w0rd
```

If successful → share mounted.

Check files:

```sh
ls /mnt/HR
```

Unmount if needed:

```sh
umount /mnt/HR
```


# Key Security Lesson

Password spraying works because:
- users reuse weak passwords
- organizations allow common passwords

Prevention:
- password complexity
- lockout policies
- user training

---

# 2. Dictionary Password Cracking

## 🎯 Goal

Crack password hashes **offline** using a dictionary.

Offline attacks are **faster** because:
- no login system involved
- billions of guesses per second possible.

# Step 1 — View Hash File

```sh
cat ms10-hashes.txt
```

Contents include:

```sh
username:RID:NTLM_hash
```

Example:

```sh
admin:500:hashvalue
guest:501:hashvalue
```

Important RIDs:

|RID|Meaning|
|---|---|
|500|Administrator|
|501|Guest|

# Step 2 — View Available Dictionaries

```sh
ls -lSr /usr/share/seclists/Passwords
```

Explanation:

|Flag|Meaning|
|---|---|
|`-l`|long format|
|`-S`|sort by size|
|`-r`|reverse order|

Dictionary chosen:

```
xato-net-10-million-passwords.txt
```

# Step 3 — Run Dictionary Attack

```sh
john --format=NT --wordlist=/usr/share/seclists/Passwords/xato-net-10-million-passwords.txt ms10-hashes.txt
```

Command explanation:

|Option|Purpose|
|---|---|
|`john`|password cracking tool|
|`--format=NT`|NTLM hash format|
|`--wordlist`|dictionary file|
|`ms10-hashes.txt`|target hashes|

## How Dictionary Attack Works

Process:
1. Take word from dictionary
2. Hash it
3. Compare with stored hash

If match → password cracked.


# Step 4 — Export Cracked Passwords

```sh
john --show --format=NT ms10-hashes.txt > dict-cracked.txt
```

Explanation:

|Part|Meaning|
|---|---|
|`--show`|display cracked passwords|
|`>`|save output|
|`dict-cracked.txt`|results file|

# Step 5 — View Results

```
less dict-cracked.txt
```

Controls:

|Key|Action|
|---|---|
|Space|next page|
|b|previous page|
|q|quit|

# Step 6 — Show Uncracked Passwords

```sh
john --show=left --format=NT ms10-hashes.txt
```

Displays accounts still not cracked.

# Key Lesson

Dictionary attacks succeed when passwords are:
- common
- reused
- predictable.


---

# 3. Brute Force Password Cracking

## 🎯 Goal

Try **every possible password combination**.

# Step 1 — Clear Previous Cracked Passwords

```sh
rm ~/.john/john.pot
```

Explanation:

|Component|Meaning|
|---|---|
|`rm`|remove file|
|`~/.john/john.pot`|stored cracked passwords|

# Step 2 — Start Brute Force Attack

```sh
john --format=NT --incremental ms10-hashes.txt
```

Explanation:

|Option|Meaning|
|---|---|
|`--incremental`|brute force mode|
|`--format=NT`|NTLM hashes|

## Brute Force Process

John tries:

```sh
a
b
c
...
aa
ab
ac
...
```

Using:
- **95 printable ASCII characters**
- password length up to **13**

# Step 3 — View Progress

Press:

```
SPACEBAR
```

Shows:
- guesses per second
- elapsed time
- candidate passwords

# Step 4 — Stop Attack

Press:

```
q
```

# Step 5 — Show Cracked Passwords

```sh
john --show --format=NT ms10-hashes.txt
```

Save results:

```sh
john --show --format=NT ms10-hashes.txt > brute-cracked.txt
```

# Step 6 — Limit Password Length

Example:

```sh
john --format=NT --incremental --max-length=6 ms10-hashes.txt
```

Explanation:

|Parameter|Meaning|
|---|---|
|`--max-length`|limit password length|

Example outcomes:

|Length|Estimated time|
|---|---|
|6 chars|hours|
|7 chars|weeks|
|8 chars|years|

# Key Lesson

Password resistance depends on:

✔ length  
✔ complexity

---

# 4. Configure Domain Password Policy

## 🎯 Goal

Improve domain security after discovering weak passwords.

# Step 1 — Login to Domain Controller

```powershell
Structureality\Administrator
Password: Pa$$w0rd
```

Open **PowerShell as Administrator**

# Step 2 — Load Active Directory Module

```powershell
Import-Module ActiveDirectory
```

Explanation:

Loads AD PowerShell tools.

# Step 3 — View Current Policy

```powershell
Get-ADDefaultDomainPasswordPolicy
```

Current settings included:

```
ComplexityEnabled: True
MinPasswordLength: 7
MaxPasswordAge: 42
LockoutThreshold: 0
```

LockoutThreshold = 0 means:

**Account lockout disabled**

# Step 4 — Configure Stronger Policy

Set observation window:

```powershell
Set-ADDefaultDomainPasswordPolicy -Identity structureality -LockoutObservationWindow 00:15:00
```

Set lockout duration:

```powershell
Set-ADDefaultDomainPasswordPolicy -Identity structureality -LockoutDuration 00:15:00
```

Set lockout attempts:

```powershell
Set-ADDefaultDomainPasswordPolicy -Identity structureality -LockoutThreshold 3
```

Set password expiration:

```powershell
Set-ADDefaultDomainPasswordPolicy -Identity structureality -MaxPasswordAge 365.00:00:00
```

Set minimum password age:

```powershell
Set-ADDefaultDomainPasswordPolicy -Identity structureality -MinPasswordAge 3.00:00:00
```

Set minimum password length:

```powershell
Set-ADDefaultDomainPasswordPolicy -Identity structureality -MinPasswordLength 12
```

# Step 5 — Verify Changes

```powershell
Get-ADDefaultDomainPasswordPolicy
```

New policy includes:

|Setting|Value|
|---|---|
|MinPasswordLength|12|
|LockoutThreshold|3|
|LockoutDuration|15 minutes|
|MaxPasswordAge|365 days|

# Optional: Force Password Change

Remove non-expiring passwords:

```powershell
Get-ADUser -Filter * | Set-ADUser -PasswordNeverExpires $False
```

Force change on login:

```powershell
Get-ADUser -Filter * | Set-ADUser -ChangePasswordAtLogon $True
```

# Final Security Concepts

### Password Spraying

Trying **limited number of known passwords across many accounts**.

Goal:  
Avoid **account lockout**.


### Dictionary Attack

Uses a **wordlist** to crack hashes.

Success depends on:  
**dictionary size and password predictability**.

### Brute Force Attack

Tries **every possible password combination**.

Defense:
- longer passwords
- complex characters.

### Strongest Password Policy Control

Most impactful setting:

✔ **Minimum Password Length**

Because password space grows exponentially.
