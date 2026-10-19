
> [!info] Why this matters for Security+? This lab is the **virtualization counterpart** to the containers lab. Where containers share a kernel, VMs get full hardware virtualization + their own isolated OS instance. The **virtual switch type** you choose (Private/Internal/External) directly determines the VM's network attack surface.

---

## 🧩 Section 1 — Install Hyper-V

### Commands

| Command                                                        | Element Breakdown                                                                                  | Purpose                                             |
| -------------------------------------------------------------- | -------------------------------------------------------------------------------------------------- | --------------------------------------------------- |
| `Install-WindowsFeature -Name Hyper-V -IncludeManagementTools` | `-Name` = feature to install; `-IncludeManagementTools` = adds Hyper-V Manager + PowerShell module | Installs the Hyper-V role + admin tools in one step |

**Steps:** Run PowerShell as Admin → install feature → restart (reason: **Other (Unplanned)**) to complete the hypervisor install.

> [!note] Why a restart is required? Hyper-V changes the boot configuration to load the **hypervisor layer** beneath the host OS itself (the host OS becomes a "parent partition"). This is a low-level architectural change, not just a service — hence the mandatory reboot.

---

## 🧩 Section 2 — Configure a New Virtual Machine

### Wizard Walkthrough (Hyper-V Manager → Actions → New → Virtual Machine)

|Wizard Page|Setting Chosen|Notes|
|---|---|---|
|Specify Name and Location|`My Lab VM`|Friendly VM identifier|
|Specify Generation|**Generation 1**|Gen 2 does **not** support 32-bit OSes — Gen 1 chosen for compatibility|
|Assign Memory|`1024` MB startup memory|Right-size memory allocation — avoid over-provisioning host resources|
|Configuring Networking|_(default, connected later)_|Network config deferred to later step|
|Connect Virtual Hard Disk|`90` GB size|Defines max VHD capacity|
|Installation Options|**Install an operating system later**|Manual ISO/media mount required in next section|


---

## 🧩 Section 3 — Install an Operating System on the VM

### Steps Summary

1. VM **Settings** → DVD Drive (IDE Controller 1) → mount **Physical CD/DVD Drive** pointing to the WinServer 2019 ISO (Drive D:)
2. **Connect** to My Lab VM → **Start** → runs Windows Setup
3. Setup flow: **Install now** → _I don't have a product key_ → **Windows Server 2019 Standard (Desktop Experience)** → accept license → **Custom: Install Windows only (advanced)** → accept default partition → installs (~5–10 min)
4. Set local Administrator password: `Pa$$w0rd`


---

## 🧩 Section 4 — Configure Virtual Networking Switches

### Virtual Switch Manager (Hyper-V Manager → Actions → Virtual Switch Manager)

| Switch Created | Type         | Name                  | Key Property                                                         |
| -------------- | ------------ | --------------------- | -------------------------------------------------------------------- |
| Switch 1       | **Private**  | `Private Lab Switch`  | No association to any physical/host adapter                          |
| Switch 2       | **External** | `External Lab Switch` | Bound to a physical host NIC (**Microsoft Hyper-V Network Adapter**) |

### Comparison Table — Hyper-V Virtual Switch Types

|Switch Type|VM ↔ VM (same switch)|VM ↔ Host|VM ↔ External Network|
|---|---|---|---|
|**Private**|✅ Yes|❌ No|❌ No|
|**Internal**|✅ Yes|✅ Yes|❌ No|
|**External**|✅ Yes|✅ Yes|✅ Yes|

> [!warning] Security use case for Private switches: A **Private** switch is a deliberate isolation tool — connect a newly-built or untrusted VM to it first to run security checks (patching, malware scans, credential hardening) **before** ever exposing it to the host or production network. This mirrors real-world "quarantine VLAN" practices.

---

## 🧩 Section 5 — Connect Virtual Machines with Virtual Networking

### Connectivity Test Walkthrough

|Step|Switch Assigned to My Lab VM|Ping Target|Result|Why|
|---|---|---|---|---|
|1|**Private Lab Switch**|`10.1.16.13` (MS10 host)|❌ **Fails**|Private switch blocks all host/external traffic — VM can only talk to peers on the _same_ private switch|
|2|**External Lab Switch**|`10.1.16.13` (MS10 host)|✅ **Succeeds**|External switch bridges to the physical host NIC — full network reachability|

### Manual IP Configuration (My Lab VM — set inside guest OS)

|Field|Value|
|---|---|
|IP address|`10.1.16.225`|
|Subnet mask|`255.255.255.0`|
|Default gateway|`10.1.16.254`|
|Preferred DNS|`10.1.16.1`|

**Path:** Right-click Start → Network Connections → Change adapter options → right-click Ethernet → Properties → double-click **IPv4** → set static IP.

### Commands Used

| Command              | Purpose                                                         |
| -------------------- | --------------------------------------------------------------- |
| `ipconfig`           | Confirm MS10's `vEthernet (External Lab Switch)` IPv4 address   |
| `Ping -4 10.1.16.13` | Test connectivity from guest VM to host across each switch type |

> [!note] Switching a VM's virtual switch requires reassignment via Settings Changing network exposure isn't just a guest-OS setting — it's done at the **hypervisor level** (VM Settings → Network Adapter → Virtual switch dropdown). This host-level control is a security strength: an admin can instantly cut a compromised VM off from the network without touching the guest OS at all.

---

## 🧩 Comprehensive Questions — Quick Reference

|#|Question|Correct Answer|
|---|---|---|
|1|PowerShell command to install Hyper-V|`Install-WindowsFeature -Name Hyper-V -IncludeManagementTools`|
|2|Create a new VM in Hyper-V Manager|Select **"New" → "Virtual Machine"**|
|3|External switch devices can connect to (select all)|**Host computer**, **other devices on the external switch**, **other devices on the network**|
|4|True statement about a Private virtual switch|VMs on a private switch can connect **only to other devices on the same private switch**|

---

## 🔑 Full Command/Action Cheat Sheet (this lab)

|Action|Command / Path|
|---|---|
|Install Hyper-V|`Install-WindowsFeature -Name Hyper-V -IncludeManagementTools`|
|Open Hyper-V Manager|Server Manager → Tools → Hyper-V Manager|
|Create new VM|Hyper-V Manager → Actions → New → Virtual Machine|
|Create virtual switch|Hyper-V Manager → Actions → Virtual Switch Manager|
|Reassign VM's network|VM → Settings → Network Adapter → Virtual switch dropdown|
|Check IP config|`ipconfig`|
|Test connectivity|`Ping -4 <target-IP>`|
