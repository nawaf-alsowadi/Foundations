

> [!info] Why this matters for Security+ Containers are a distinct **architecture model** (3.1) — lightweight, share the host kernel, and have their own isolated OS/user/network context. Understanding container isolation boundaries (and their limits) is core exam material for comparing containers vs. VMs vs. serverless.

---

## 🧩 Section 1 — Environment Verification

**Goal:** Confirm Docker Engine is installed/running and identify host details.

### Commands

| Command    | Purpose                                                           |
| ---------- | ----------------------------------------------------------------- |
| `ipconfig` | Lists IP configuration of host adapters                           |
| `Winver`   | Displays OS build/version info (confirms Server 2019, build 1809) |
| `hostname` | Confirms the VM's computer name                                   |

> [!tip] Services check: Docker Engine status is verified via **Services.msc** → look for `Docker Engine` → Status = `Running`. This is a GUI-based service health check, an alternative to `Get-Service docker`.

> [!note] Version matters ! The container image tag (`:1809`) must match the **host OS build**. Windows containers require kernel version parity between host and container base image — this is a key architectural difference from Linux containers, which don't have this constraint.

---

## 🧩 Section 2 — Managing Docker Containers with PowerShell

### Core Command Reference

|Command|Element Breakdown|Effect|
|---|---|---|
|`docker images`|Lists local image cache|Shows `mcr.microsoft.com/windows/nanoserver` pre-loaded locally|
|`docker create --name MyFirstContainer mcr.microsoft.com/windows/nanoserver:1809`|`create` = provision without starting; `--name` = friendly identifier; `:1809` = local tag|Creates container in **Created** state (not running)|
|`docker ps -a`|`ps` = process status; `-a` = include stopped/non-running containers|Lists **all** containers regardless of state|
|`docker start MyFirstContainer`|`start` = launch existing container|Transitions Created → attempts run → **Exited** (no foreground process)|
|`docker run -it --name TestContainer mcr.microsoft.com/windows/nanoserver:1809`|`run` = create + start in one step; `-i` = interactive (keep STDIN open); `-t` = allocate pseudo-TTY|Creates **and** attaches an interactive shell session|

> [!warning] Why MyFirstContainer exited? A container's lifecycle is tied to its **main process**. `docker create` + `docker start` with no interactive flag and no long-running process/entrypoint means the container has nothing to keep it alive — it starts, has no work to do, and exits immediately. This is different from a VM, which stays "on" regardless of running processes.

### Comparison Table — `docker create` vs `docker run -it`

| Aspect          | `docker create` → `docker start`    | `docker run -it`                   |
| --------------- | ----------------------------------- | ---------------------------------- |
| Steps           | 2 (create, then start)              | 1 (combined)                       |
| Interactivity   | None by default                     | Interactive shell attached         |
| Resulting state | Exited (no process to hold it open) | Running, with a live session       |
| Use case        | Pre-staging containers              | Ad hoc/interactive troubleshooting |

### In-Container Investigation

| Command          | Purpose                                                       |
| ---------------- | ------------------------------------------------------------- |
| `ver`            | OS version string inside the container                        |
| `ipconfig`       | Container's own IP config (via `vEthernet (Ethernet)`)        |
| `hostname`       | Container has its **own** computer name — distinct from host  |
| `netstat -aon`   | Lists listening ports — checked here for TCP **5985** (WinRM) |
| `ping <gateway>` | Confirms NAT gateway reachability                             |
| `ping <host-IP>` | Confirms container ↔ host communication over NAT              |

> [!important] Networking takeaway (Security+ relevant): By default, new Windows containers attach to a **NAT network**, which permits:
> 
> - Container → Gateway communication
> - Container → Host communication
> - Container → Container communication (same NAT)
> 
> This default openness is a **security consideration** — containers should be evaluated for network segmentation just like any other host on the network. Don't assume isolation by default.

---

## 🧩 Section 3 — Users and Groups Inside a Container

### Command Reference

| Command                                                           | Element Breakdown                                                                                 | Purpose                                                 |
| ----------------------------------------------------------------- | ------------------------------------------------------------------------------------------------- | ------------------------------------------------------- |
| `docker exec -it TestContainer cmd`                               | `exec` = run command in a **running** container; `-it` = interactive TTY; `cmd` = shell to launch | Enters a live container session                         |
| `echo %username%`                                                 | Environment variable expansion                                                                    | Shows current session's username                        |
| `net user`                                                        | Enumerates local users                                                                            | Lists container's local accounts                        |
| `net user Administrator`                                          | Targeted account query                                                                            | Shows Administrator account status (enabled/active)     |
| `net localgroup`                                                  | Enumerates local groups                                                                           | Confirms presence of **Docker Administrators** group    |
| `docker exec -it --user ContainerAdministrator TestContainer cmd` | `--user` flag overrides default exec identity                                                     | Enters session as **ContainerAdministrator** (elevated) |
| `net user testuser pa$$word123 /ADD`                              | `/ADD` switch                                                                                     | Creates new local user in the container                 |
| `net localgroup "Power Users" testuser /add`                      | Group name in quotes (contains space)                                                             | Adds user to a local group                              |
| `net user Administrator pa$$word123 /passwordchg:yes`             | `/passwordchg:yes`                                                                                | Sets password + allows user-initiated password changes  |

> [!info] Default identity vs. ContainerAdministrator: Regular `docker exec -it <container> cmd` sessions may run under a limited context. Adding `--user ContainerAdministrator` elevates privileges within the container — required for actions like user/group management. This mirrors the real-world principle of **least privilege**: don't operate as admin unless the task requires it.

> [!important] Security best practice reinforced in-lab: Containers maintain **entirely separate** users, groups, OS, and network identity from the host. Before promoting a container image to production:
> 
> - Set strong, unique passwords (default/blank creds are a common attack vector)
> - Review local group memberships
> - Don't assume host hardening applies to the container — it doesn't inherit host security posture automatically

---

## 🧩 Section 4 — Data Persistence in Containers

### Command Reference

|Command|Purpose|
|---|---|
|`Dir`|Lists current directory contents|
|`md MyFolder`|Creates a new folder (`md` = make directory)|
|`cd MyFolder`|Changes working directory|
|`echo "text" > test.txt`|Redirects output to create a file with content|
|`type test.txt`|Displays file contents|
|`exit`|Leaves container shell, returns to host|
|`docker stop TestContainer`|Gracefully stops the running container|

> [!note] Persistence behavior — key exam concept **Stopping** a container ≠ deleting it. The container's writable layer (filesystem changes) **persists** across stop/start cycles. Data is only lost when the container is explicitly removed via `docker rm`.
> 
> |Action|Filesystem Changes|
> |---|---|
> |`docker stop`|Preserved|
> |`docker start` (after stop)|Preserved data still present|
> |`docker rm`|**Deleted**|

> [!warning] Stop containers gracefully: Improperly terminating containers (e.g., killing the host process instead of `docker stop`) risks **data corruption** and potential security exposure from an unclean shutdown state.

---

## 🧩 Comprehensive Questions — Quick Reference

| #   | Question                                   | Correct Command/Answer                    |
| --- | ------------------------------------------ | ----------------------------------------- |
| 1   | Stop a container session                   | `docker stop MyContainer`                 |
| 2   | Build image from Dockerfile in current dir | `docker build -t my-dummy-image .`        |
| 3   | List all available images                  | `docker images`                           |
| 4   | Start a session for an existing container  | `docker start MyContainer`                |
| 5   | Specify a local image for building         | Include the tag listed by `docker images` |

> [!tip] `docker build -t` breakdown
> 
> - `build` = construct image from a Dockerfile
> - `-t` = tag the resulting image with a name
> - `.` = build context = current directory (where Dockerfile lives)

---

## 🔑 Full Command Cheat Sheet (this lab)

| Command                                      | One-liner                                   |
| -------------------------------------------- | ------------------------------------------- |
| `docker images`                              | List local images                           |
| `docker create --name <name> <image>:<tag>`  | Create container (not started)              |
| `docker ps -a`                               | List all containers, any state              |
| `docker start <name>`                        | Start existing container                    |
| `docker run -it --name <name> <image>:<tag>` | Create + start interactively                |
| `docker exec -it <name> cmd`                 | Attach shell to running container           |
| `docker exec -it --user <user> <name> cmd`   | Attach shell as specific user               |
| `docker stop <name>`                         | Gracefully stop container                   |
| `docker rm <name>`                           | Delete container (and its filesystem layer) |
| `docker build -t <tag> .`                    | Build image from Dockerfile in current dir  |

---

## 🧠 Security Notes

> [!important] Ties to 3.1 / 3.2
> 
> - **3.1 Architecture models:** Containers share the host kernel (unlike VMs, which virtualize hardware and run full guest OSes). This means a **container escape has a more direct path to host compromise than a VM escape** — a key point when comparing security implications across architecture models.
> - **3.2 Secure enterprise infrastructure:** Default NAT networking, default credentials, and group memberships inside container images are all things that must be **hardened before production deployment** — treat container images with the same rigor as golden VM images/baselines.
********