Record events each time an event has occurred & identifies attempted or successful attack. Operate during attack.

## Enable Auditing on Folders & Files Deletion

Steps:
1. Right Click on folder/file => Properties
2. Security => Advanced ![[Pasted image 20260225013026.png]]
3. Auditing => Add![[Pasted image 20260225013123.png]]
4. Select the who will be audited: Select a Principal => Type "Everyone" in object name => Check Names![[Pasted image 20260225013251.png]]
5. Show Advanced Permission => chose the actions that need to be audited![[Pasted image 20260225013423.png]]
6. Select the audit entry and mark the **Replace all child object auditing…** settings checkbox.![[Pasted image 20260225013619.png]]
## Event Viewer
Steps:
1. Event Viewer
2. Windows Logs
3. Security
4. Click first event
5. Click find and search for the string/event ID

## Most Important Event IDs for Security

Example: Event ID 4660 (object deletion) to confirm **deleting a file**, the **file name** will be found with event code 4663 after (above in event viewer) the event 4660.

| Serial Number | Category            | Event ID and Description                                 | Reasons to Monitor (by no means exhaustive)                            |
| ------------- | ------------------- | -------------------------------------------------------- | ---------------------------------------------------------------------- |
| 1             | Authentication      | **4624** – Successful logon                              | Detect unusual logon times, privileged account usage, lateral movement |
| 2             | Authentication      | **4625** – Failed logon                                  | Brute-force attacks, password spraying, credential stuffing            |
| 3             | Authentication      | **4634** – Logoff                                        | Track session duration, suspicious short-lived sessions                |
| 4             | Authentication      | **4648** – Logon with explicit credentials               | Pass-the-hash, runas abuse, lateral movement                           |
| 5             | Authentication      | **4672** – Special privileges assigned to new logon      | Admin-level access granted                                             |
| 6             | Account Management  | **4720** – User account created                          | Rogue account creation                                                 |
| 7             | Account Management  | **4722** – User account enabled                          | Re-enabling disabled accounts                                          |
| 8             | Account Management  | **4723** – Password change attempt                       | Unauthorized password change                                           |
| 9             | Account Management  | **4724** – Password reset attempt                        | Account takeover indicator                                             |
| 10            | Account Management  | **4726** – User account deleted                          | Covering tracks                                                        |
| 11            | Group Management    | **4732 / 4728** – Member added to security-enabled group | Privilege escalation (Domain Admins, etc.)                             |
| 12            | Group Management    | **4733 / 4729** – Member removed from security group     | Unauthorized privilege modification                                    |
| 13            | Policy Change       | **4719** – System audit policy changed                   | Disabling logging before attack                                        |
| 14            | Policy Change       | **4739** – Domain policy changed                         | Weakening security posture                                             |
| 15            | Log Integrity       | **1102** – Audit log cleared                             | Attempt to hide activity                                               |
| 16            | Log Integrity       | **1100** – Event log service shutdown                    | Logging disabled                                                       |
| 17            | Kerberos            | **4768** – TGT requested                                 | Kerberoasting detection                                                |
| 18            | Kerberos            | **4769** – Service ticket requested                      | Lateral movement, Kerberoasting                                        |
| 19            | Kerberos            | **4771** – Kerberos pre-auth failed                      | Password spraying                                                      |
| 20            | NTLM                | **4776** – NTLM authentication attempt                   | NTLM abuse detection                                                   |
| 21            | RDP                 | **4624 (Logon Type 10)** – Remote interactive logon      | RDP access monitoring                                                  |
| 22            | System Startup      | **6005** – Event log service started                     | System boot                                                            |
| 23            | System Shutdown     | **6006** – Event log service stopped                     | Graceful shutdown                                                      |
| 24            | Unexpected Shutdown | **6008** – Unexpected shutdown                           | Crash, forced reboot                                                   |
| 25            | System Restart      | **1074** – Restart initiated                             | Who/what triggered reboot                                              |
| 26            | Service Control     | **7045** – New service installed                         | Persistence mechanism                                                  |
| 27            | Service Control     | **7036** – Service state changed                         | Service stopped/started unexpectedly                                   |
| 28            | Windows Defender    | **1116** – Malware detected                              | Confirmed threat detection                                             |
| 29            | Windows Defender    | **1117** – Malware remediation                           | Cleanup activity                                                       |
| 30            | Windows Defender    | **5007** – Configuration changed                         | AV tampering                                                           |
| 31            | Process Creation    | **4688** – New process created                           | Suspicious parent-child processes                                      |
| 32            | Process Termination | **4689** – Process exited                                | Malware cleanup patterns                                               |
| 33            | File Share Access   | **5140** – Network share accessed                        | Lateral movement                                                       |
| 34            | File Share Detailed | **5145** – Share object checked                          | Data exfiltration                                                      |
| 35            | Object Access       | **4663** – Attempt to access object                      | Sensitive file access                                                  |
| 36            | Registry            | **4657** – Registry value modified                       | Persistence via registry                                               |
| 37            | PowerShell          | **4104** – Script block logging                          | Detect obfuscated or malicious scripts                                 |
| 38            | PowerShell          | **4103** – Module logging                                | Suspicious module usage                                                |
| 39            | Object Deletion     | **4460** - Attempt to delete object                      | Sensitive file deletion                                                |
