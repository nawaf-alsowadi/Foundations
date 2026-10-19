
# PART 1 — Using Hashing to Confirm File Download Integrity

## 🎯 Objective
Verify that a downloaded file has not been altered by comparing its hash to the expected hash published by the source.
## Step 1 — Identify the Expected Hash

On a local browser:
1. Visit:  
    `http://dftt.sourceforge.net/`
2. Open **EXT3FS Keyword Search Test ## 1**
3. Locate the published **MD5 hash** of:
    ```
    ext3-img-kw-1.dd
    ```
### Important Concept
Hash length reveals algorithm:

| Algorithm | Bit Length | Hex Length |
| --------- | ---------- | ---------- |
| MD5       | 128        | 32         |
| SHA-1     | 160        | 40         |
| SHA-256   | 256        | 64         |

✔ MD5 bit length = **128 bits (4 * 32)**

## Step 2 — Prepare File in Kali
1. Log into Kali as root.
2. Mount `Student-Resources-L06.ISO`.
3. Copy files:

```bash
cp /media/cdrom0/* /root/Downloads/
```
4. Extract archive:

```bash
cd /root/Downloads
unzip 4-kwsrch-ext3.zip
```
5. Verify file:

```bash
ls -l 4-kwsrch-ext3
```

Confirm:
```
ext3-img-kw-1.dd
Size: 5242880 bytes
```

## Step 3 — Compare Hash Values

View expected hash file:
```bash
cat 4-kwsrch-ext3-hash.txt
```

Generate local hash:
```bash
md5sum 4-kwsrch-ext3/ext3-img-kw-1.dd >> 4-kwsrch-ext3-hash.txt
```

View both:
```bash
cat 4-kwsrch-ext3-hash.txt
```

If hashes match exactly →  
✔ File integrity confirmed  
✔ File not altered

## 🔐 Why This Works

Hashing produces:
- Fixed-length output
- Unique fingerprint of file content

If even 1 bit changes → hash changes completely.

## ⚠ MD5 Deprecation

**MD5** is weak due to:
- **Collision vulnerability**

Better modern alternatives:
- `sha256sum`
- `sha512sum`

But integrity verification process remains **identical**.

# PART 2 — Malware Evaluation Using MetaDefender

## 🎯 Objective

Analyze a suspicious file via hash lookup instead of uploading the file.

## Step 1 — Generate SHA1 Hash

In Kali:
```bash
cd /usr/share/windows-resources/binaries
sha1sum nc.exe
```

Expected result:
```
57F0839433234285CC9DF96198A6CA58248A4707
```

## Step 2 — Use Online Hash Lookup

On local browser:
1. Go to:
    ```
    metadefender.com
    ```
2. Paste hash.
3. Select **Process**.

## Interpretation

MetaDefender:
- Queries multiple AV engines.
- Shows detection results.

Important insight:
- **Not all detections mean malware.**
- Could be:
    - Admin tool
    - Hacker tool
    - Potentially Unwanted Program (**PUP**)

✔ **Hash lookup avoids uploading file ** 
✔ Maintains confidentiality

## Key Principle

Hash-based lookup benefits:
- Does not disclose file publicly
- Faster analysis
- Useful for known threats
  
# PART 3 — Exploring Salting in Password Hashing

## 🎯 Objective

Demonstrate how salting increases resistance to brute force attacks.

## Step 1 — Observe Linux Salted Hashes

```bash
grep '\$' /etc/shadow
```

Modern Linux:
- Uses **yescrypt**
- Format:

```
$algorithm$hash-parameters$salt$hash
```

## Step 2 — Create Unsalted Hash

```bash
openssl passwd -salt "" pass1 > hash.txt
```

View:
```bash
cat hash.txt
```

Note:
- No salt between 2nd and 3rd `$`

## Step 3 — Crack Unsalted Hash

```bash
john -incremental hash.txt
```

Result:  
✔ Cracked in <10 seconds

Reason:
- Only 5-character password
- Small keyspace

## Step 4 — Create Salted Hash

```bash
openssl passwd -salt SALT pass1 > salted-hash.txt
```

View:
```bash
cat salted-hash.txt
```

Now contains:
```
$1$SALT$hashvalue
```

## Step 5 — Crack Salted Hash (Salt Known)

```bash
john -incremental salted-hash.txt
```

Cracks quickly because:
- **Salt is included in file**
- **John knows the salt**

## Step 6 — Remove Salt from File

```bash
cat salted-hash.txt | sed "s/SALT//g" > salt-secret-hash.txt
```

Now John does NOT know salt.

## Step 7 — Crack Salted Hash (Salt Unknown)

```bash
john -incremental salt-secret-hash.txt
```

Estimated time:  
~814,506,250 seconds (~25.8 years)

## Why?

Original password length: 5 characters  
Salt adds 4 characters

Total effective length: 9 characters

With 95-character charset:

```
95^4 ≈ 81 million multiplier increase
```

Thus:

```
10 seconds × 95^4 = ~25.8 years
```

---

# 🔎 Key Security Lessons

## Purpose of Hashing Files

✔ Detecting integrity violations

## Benefit of Salting

✔ Increases resistance to brute force attacks  
✔ Prevents rainbow table attacks  
✔ Makes identical passwords hash differently

## Why Avoid MD5?

✔ High collision probability

## Why Use Hash Lookup Instead of Upload?

✔ Avoid disclosing suspicious file publicly

## If Some Engines Flag a File, Is It Malicious?

✔ No — may be dual-use or PUP

# 🧠 Final Conceptual Takeaways

### Hashing = Integrity verification

### Salting = Anti-brute-force mechanism

### Collision resistance = Cryptographic strength

### Hash reputation services = Threat intelligence shortcut
