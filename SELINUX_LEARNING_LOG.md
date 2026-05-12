# SELinux Learning Log
### A 73-Module Curriculum: From Linux Basics to Automotive ECU Security

**Repository:** SELinux Userspace (v3.10) — `github.com/SELinuxProject/selinux`
**Branch:** `claude/learn-selinux-QSNyz`
**Target Platform:** Xen + ARMv8-based Automotive ECUs

---

## How to Use This Log

1. **Resuming a session:** Tell Claude — *"I am following the SELinux learning log. My progress is in `SELINUX_LEARNING_LOG.md`. Please resume from the next unchecked module."*
2. **Marking a module done:** Change `- [ ]` to `- [x]` next to the module name.
3. **Adding notes:** Use the Session Notes section at the bottom.
4. **Each module covers:** Why it exists · When to use it · How to configure it · What it protects against · Boot & runtime behaviour · Attack scenario · Intrusion detection use · Forensics use.

---

## Teaching Approach

- **Target audience:** Complete novice to Linux and programming
- **Style:** Every term explained from scratch, with real-world analogies
- **ECU context:** All examples tied to Xen + ARMv8 automotive scenarios (CAN bus, OTA, V2X, UDS/OBD-II diagnostics, ASIL safety levels)
- **Delivery:** One module per message. Say **"next"** to advance.

---

## Progress Overview

```
Phase 0  — Linux Foundations          5 modules   [ 2 / 5  done ]
Phase 1  — SELinux Concepts           7 modules   [ 0 / 7  done ]
Phase 2  — CIL Language               6 modules   [ 0 / 6  done ]
Phase 3  — User, Role, Type, Level    6 modules   [ 0 / 6  done ]
Phase 4  — Classes & Permissions      4 modules   [ 0 / 4  done ]
Phase 5  — Access Vector Rules        9 modules   [ 0 / 9  done ]
Phase 6  — Labeling Everything        9 modules   [ 0 / 9  done ]
Phase 7  — Conditionals & Booleans    3 modules   [ 0 / 3  done ]
Phase 8  — Building & Deploying       6 modules   [ 0 / 6  done ]
Phase 9  — Automotive / ARMv8 / Xen  10 modules   [ 0 / 10 done ]
Phase 10 — Advanced Topics            5 modules   [ 0 / 5  done ]
Phase 11 — Debug, Analysis, Forensics 7 modules   [ 0 / 7  done ]
──────────────────────────────────────────────────────────────────
Total                                73 modules   [ 2 / 73 done ]
```

---

## Phase 0 — Linux Foundations
*Prerequisites: how Linux works before SELinux is introduced*

- [x] **0.1** — How Linux Really Works
  - Kernel, user space, system calls, processes, PIDs, fork, execve, file descriptors, MMU, ARMv8 EL0/EL1
  - Key analogy: toll booth on a highway = SELinux hook inside the kernel

- [x] **0.2** — The Linux File System: Everything Is a File
  - Directory tree (`/`), 7 file types, `/proc`, `/sys`, `/dev`, inodes, xattrs, `security.selinux`, squashfs, mounting, `unlabeled_t`, genfscon
  - Key analogy: sealed vacuum-packed box = squashfs (read-only, cannot modify)

- [ ] **0.3** — Users, Groups & DAC Permissions
  - UIDs, GIDs, `root`, `rwxr-xr-x`, `chmod`, `chown`, setuid/setgid bits, why DAC alone fails on ECUs

- [ ] **0.4** — Processes & Privileges
  - PID lifecycle, `/proc/PID/`, capabilities (splitting root into fine-grained privileges), `CAP_NET_ADMIN`, `CAP_SYS_ADMIN`, why capabilities still aren't enough

- [ ] **0.5** — Linux Boot Sequence
  - BIOS/UEFI → bootloader (U-Boot/GRUB) → kernel decompression → init/systemd → where SELinux policy is loaded in this chain

---

## Phase 1 — SELinux Concepts
*The mental model: what SELinux is and how it thinks*

- [ ] **1.1** — What Is Mandatory Access Control?
  - DAC vs MAC, discretionary vs mandatory, why even root must be constrained, the principle of least privilege

- [ ] **1.2** — What Is SELinux?
  - History (NSA, 2000), LSM framework, where SELinux sits in the kernel, how it differs from AppArmor/SMACK, why it is used in automotive

- [ ] **1.3** — The Security Context
  - `user:role:type:level` — anatomy of a label, `system_u`, `object_r`, `system_r`, `s0`, reading labels with `ls -Z` and `ps -Z`

- [ ] **1.4** — SELinux Modes
  - Enforcing, Permissive, Disabled — what each means, why Disabled is never acceptable on production ECU, using Permissive for policy development

- [ ] **1.5** — The Policy
  - What a policy file is, binary vs source policy, where it lives on disk (`/etc/selinux/`), when it is loaded, what happens if it is missing

- [ ] **1.6** — The Access Vector Cache (AVC)
  - Why the kernel caches decisions, how the AVC works, cache hits vs misses, AVC denial log generation, performance implications on ARMv8

- [ ] **1.7** — SELinux on Embedded / ECU / ARMv8 Systems
  - `DISABLE_SETRANS`, Yocto/Buildroot packaging, immutable policy on read-only rootfs, memory footprint, Xen + SELinux coexistence

---

## Phase 2 — CIL Language Fundamentals
*Reading and writing SELinux policy source code*

- [ ] **2.1** — CIL Introduction
  - What CIL is, why it replaced M4, s-expression design, human-readable source → binary for kernel, repo file: `secilc/docs/cil_introduction.md`

- [ ] **2.2** — CIL Syntax & Structure
  - S-expression syntax `(keyword arg1 arg2)`, comments with `;`, symbol rules, whitespace, declarations vs definitions, repo file: `secilc/docs/cil_reference_guide.md`

- [ ] **2.3** — Namespaces, Blocks & `in` Statements
  - `(block ...)`, `(blockabstract ...)`, `(blockinherit ...)`, `(in ...)`, dot notation, namespace resolution, repo file: `secilc/docs/cil_container_statements.md`, `secilc/test/block_test.cil`

- [ ] **2.4** — Macros (`call` / `macro`)
  - Reusable policy patterns, `(macro name (params) body)`, `(call name (args))`, repo file: `secilc/docs/cil_call_macro_statements.md`

- [ ] **2.5** — Optional Blocks
  - `(optional ...)`, graceful degradation when dependencies are missing, repo file: `secilc/test/optional_test.cil`

- [ ] **2.6** — `minimum.cil` Line by Line
  - Walk through the smallest valid policy in the repo, every statement explained, repo file: `secilc/test/minimum.cil`

---

## Phase 3 — The Four Pillars: User, Role, Type, Level
*The building blocks every security context is made of*

- [ ] **3.1** — Types & Typealiases
  - `(type name)`, `(typealias name alias)`, `(typeattribute ...)`, what a type represents, naming conventions (`_t` suffix)

- [ ] **3.2** — Attributes
  - `(attribute name)`, `(typeattributeset attr (type1 type2 ...))`, grouping types for policy efficiency, repo file: `secilc/docs/cil_type_statements.md`

- [ ] **3.3** — Roles
  - `(role name)`, `(roletype role type)`, `(roleallow ...)`, `(roletransition ...)`, role-based access in an ECU context, repo file: `secilc/docs/cil_role_statements.md`

- [ ] **3.4** — Users
  - `(user name)`, `(userrole user role)`, SELinux users vs Linux UIDs, `system_u` for daemons, repo file: `secilc/docs/cil_user_statements.md`

- [ ] **3.5** — Sensitivity & Categories (MLS/MCS)
  - `(sensitivity ...)`, `(category ...)`, `(level ...)`, `s0:c0,c1`, mapping ASIL-A/B/D to MLS levels, repo file: `secilc/docs/cil_mls_labeling_statements.md`

- [ ] **3.6** — Bounds
  - `(typebounds parent child)`, what it means for a type to be bounded by another, repo file: `secilc/test/bounds.cil`

---

## Phase 4 — Classes & Permissions
*The vocabulary SELinux uses to describe every possible operation*

- [ ] **4.1** — Object Classes
  - `file`, `dir`, `chr_file`, `blk_file`, `lnk_file`, `fifo_file`, `sock_file`, `process`, `socket`, `capability` — every class and what it represents, repo file: `secilc/docs/cil_class_and_permission_statements.md`

- [ ] **4.2** — Permissions
  - `read`, `write`, `open`, `execute`, `create`, `unlink`, `getattr`, `setattr`, `ioctl`, `lock`, `append` — what each permission means, repo file: `libsepol/include/sepol/policydb/flask_types.h`

- [ ] **4.3** — Common Permission Sets
  - `(common name (perm1 perm2 ...))`, shared permission groups across classes, how they reduce duplication

- [ ] **4.4** — ECU-Relevant Classes
  - `chr_file` for CAN (`/dev/can0`), `blk_file` for flash (`/dev/mmcblk0`), `process` for daemon isolation, `unix_stream_socket` for IPC, `netlink_socket` for CAN networking, `allowxperm` for ioctl command filtering

---

## Phase 5 — Access Vector Rules
*The actual allow/deny logic — the heart of every policy*

- [ ] **5.1** — `allow` Rules
  - `(allow source target (class (perm ...)))`, syntax, semantics, implicit deny (default is deny everything), repo file: `secilc/docs/cil_access_vector_rules.md`

- [ ] **5.2** — `auditallow` Rules
  - Log an access even when permitted, using allowed-but-audited for intrusion detection, `auditallow` does NOT grant permission — it only adds logging

- [ ] **5.3** — `dontaudit` Rules
  - Suppress noisy denials from cluttering logs, when to use vs when it hides real attacks, the audit2allow trap

- [ ] **5.4** — `neverallow` Rules
  - Compile-time prohibition, cannot be overridden at runtime, the safety guarantee mechanism, `seinfo --neverallow`, repo file: `secilc/test/neverallow.cil`

- [ ] **5.5** — `deny` Rules (CIL)
  - CIL's explicit deny statement, interaction with allow rules, repo files: `secilc/test/deny_rule_test1.cil`, `secilc/test/deny_rule_test2.cil`

- [ ] **5.6** — Type Transitions
  - `(typetransition source target class new_type)`, automatic type change on file creation, `(typetransition source exec_t process new_domain)` on exec

- [ ] **5.7** — Domain Transitions
  - How a process changes its type when it exec's a new program, entrypoint types, the `process { transition }` permission, why systemd → ota_t happens at execve

- [ ] **5.8** — File Transitions
  - Automatic labeling of newly created files based on creating domain and parent directory, `(typetransition creator dir file new_label)`

- [ ] **5.9** — Constraints & Validatetrans
  - `(constrain ...)`, `(validatetrans ...)`, conditional permission checks beyond allow rules, MLS write-up constraints, `dominance` relationships

---

## Phase 6 — Labeling Everything
*Applying security contexts to every object on the system*

- [ ] **6.1** — File Context Labeling
  - `file_contexts` format, regex path matching, `restorecon`, `setfiles`, `matchpathcon`, repo file: `secilc/docs/cil_file_labeling_statements.md`, `policycoreutils/setfiles/`

- [ ] **6.2** — Genfs Contexts
  - Labeling `/proc`, `/sys`, `/dev` — virtual filesystems with no xattr storage, `(genfscon fs path label)`, repo file: `secilc/docs/cil_file_labeling_statements.md`

- [ ] **6.3** — Initial SIDs
  - Labels assigned before any filesystem is available (earliest kernel boot), `(sid name)`, `(sidcontext name context)`, repo file: `secilc/docs/cil_sid_statements.md`

- [ ] **6.4** — Network Port Labeling
  - `(portcon protocol port context)`, controlling which process can bind/connect to which port, DoIP port 13400 for diagnostics, repo file: `libsemanage/tests/test_port.cil`

- [ ] **6.5** — Network Interface Labeling
  - `(netifcon interface context packet_context)`, labeling `eth0`, `can0` network interfaces, repo file: `libsemanage/tests/test_iface.cil`

- [ ] **6.6** — Network Node Labeling
  - `(nodecon address mask context)`, labeling IP address ranges, isolating ECU network zones

- [ ] **6.7** — IPC Labeling
  - Unix domain sockets, named pipes, shared memory, POSIX message queues — labeling IPC channels between daemons

- [ ] **6.8** — InfiniBand Labeling
  - `(ibendportcon ...)`, `(ibpkeycon ...)`, high-speed interconnects, repo file: `secilc/docs/cil_infiniband_statements.md`, `libsemanage/tests/test_ibendport.cil`

- [ ] **6.9** — Xen / Hypervisor Labeling
  - `(iomemcon ...)`, `(ioportcon ...)`, `(pcidevicecon ...)`, `(pirqcon ...)`, `(devicetreecon ...)`, labeling hardware resources in Xen guests, repo file: `secilc/docs/cil_xen_statements.md`

---

## Phase 7 — Conditionals & Runtime Flexibility
*Policy that can adapt without a reboot*

- [ ] **7.1** — Booleans
  - `(boolean name default)`, `(booleanif name true_block false_block)`, `setsebool`, runtime policy switching without recompile, repo file: `libsemanage/tests/test_bool.cil`

- [ ] **7.2** — Conditional Blocks (`booleanif`)
  - Writing policy that branches on boolean state, `(booleanif ...)` syntax, what can and cannot be inside a conditional, repo file: `secilc/docs/cil_conditional_statements.md`

- [ ] **7.3** — Tunables
  - Build-time policy configuration, `(tunable name default)`, `(tunableif ...)`, difference between booleans (runtime) and tunables (compile-time)

---

## Phase 8 — Building & Deploying Policy
*From CIL source to a running ECU*

- [ ] **8.1** — Policy Architecture
  - Monolithic policy vs modular policy, policy store (`/var/lib/selinux/`), how modules are linked into a base policy, policy versions

- [ ] **8.2** — Compiling with `secilc`
  - `secilc` CLI usage, input files, output binary, policy version numbers, error messages and how to read them, repo: `secilc/secilc.c`

- [ ] **8.3** — Policy Modules & `semodule`
  - `semodule -i`, `semodule -r`, `semodule -l`, `.pp` package format, how modules are managed by `libsemanage`, repo: `policycoreutils/semodule/`

- [ ] **8.4** — Yocto Integration
  - `meta-selinux` layer, `DISTRO_FEATURES += "selinux"`, `IMAGE_INSTALL`, `file_contexts` generation, baking policy binary into rootfs image

- [ ] **8.5** — `setfiles` & `restorecon`
  - When and why to relabel, `setfiles -r /mnt/rootfs /etc/selinux/targeted/contexts/files/file_contexts`, first-boot relabeling on an ECU, repo: `policycoreutils/setfiles/`

- [ ] **8.6** — `sestatus` & Runtime Inspection
  - `sestatus`, `seinfo`, `sesearch`, `getsebool -a`, reading `/sys/fs/selinux/`, checking what is currently enforced, repo: `policycoreutils/sestatus/`

---

## Phase 9 — Automotive / ARMv8 / Xen Application
*Everything applied to real ECU hardware and threats*

- [ ] **9.1** — ARMv8 Architecture Basics
  - Exception Levels (EL0 apps, EL1 kernel, EL2 hypervisor, EL3 secure monitor), TrustZone (Secure World vs Normal World), MMU and SMMU, how hardware enforces the boundaries SELinux builds on

- [ ] **9.2** — Xen on ARMv8
  - Xen hypervisor architecture, Dom0 (privileged Linux), DomU (guest VMs), XSM/Flask (Xen's SELinux equivalent), vCPUs, memory partitioning, device assignment (passthrough)

- [ ] **9.3** — ECU Threat Modeling with SELinux + Xen
  - STRIDE applied to an ECU, attack surfaces (OTA, V2X, OBD-II, CAN), trust boundaries between Xen domains, SELinux domains per ECU subsystem, defence-in-depth layers

- [ ] **9.4** — CAN Bus Isolation on ARMv8
  - Labeling `/dev/can0` as `can_device_t`, `neverallow` rules to prevent non-CAN domains from touching the bus, physical CAN controller passthrough to Xen guest, `ioctl` filtering for CAN-specific commands

- [ ] **9.5** — OTA Update Security
  - OTA client domain isolation (`ota_t`), signing key protection, firmware staging partition labeling, verified writes to `/dev/mmcblk0p3`, neverallow from `ota_t` to safety-critical domains

- [ ] **9.6** — Diagnostic Port (UDS / OBD-II) Isolation
  - DoIP/UDS port 13400 labeled `diag_port_t`, diagnostic daemon domain `diag_t`, boolean-controlled diagnostic mode, preventing diagnostic commands from reaching safety-critical actuators

- [ ] **9.7** — HSM, TrustZone & Key Store Protection
  - `/dev/tpm*` and `/dev/i2c-*` labeling, `keymgr_t` domain isolation, TrustZone secure world for key operations, preventing ptrace of key management processes, `neverallow` on key store files

- [ ] **9.8** — ASIL Domain Isolation with MCS on ARMv8
  - Mapping ASIL-A/B/C/D to MCS categories (`c0`–`c3`), sensitivity levels for safety integrity, information flow constraints (ASIL-B cannot write to ASIL-D domain), `mcstrans` rendering

- [ ] **9.9** — Secure Boot + SELinux Policy Integrity on ARMv8
  - U-Boot measured boot, dm-verity on rootfs, IMA (Integrity Measurement Architecture), how to ensure the policy binary has not been tampered with, TPM-backed attestation chain

- [ ] **9.10** — Xen XSM Flask Policy for Guest VM Isolation
  - XSM Flask policy syntax, labeling Xen domains (Dom0, DomU-CAN, DomU-OTA), controlling which VM can map which memory page, device assignment policy, Dom0 disaggregation

---

## Phase 10 — Advanced Topics
*SELinux at full capability*

- [ ] **10.1** — Sandboxing
  - `sandbox` utility, `seunshare` (namespace unsharing), sandbox domain types (`sandbox_t`, `sandbox_net_t`), cgroup resource limits, running third-party ECU apps in confinement, repo: `sandbox/`

- [ ] **10.2** — eBPF + SELinux
  - `bpf` object class, `(allow domain_t self (bpf (map_create prog_load ...)))`, `POLICYDB_CAP_BPF_TOKEN_PERMS`, BPF LSM coexistence, eBPF for observability inside SELinux-confined domains, repo: `libsepol/include/sepol/policydb/polcaps.h`

- [ ] **10.3** — systemd + SELinux
  - `SELinuxContext=` in unit files, how systemd triggers domain transitions at service start, `systemd_t` domain, socket activation and label inheritance

- [ ] **10.4** — D-Bus + SELinux
  - `dbus` component in repo, `(allow sender_t bus_t (dbus (send_msg)))`, controlling method calls over the system bus, repo: `dbus/`

- [ ] **10.5** — `mcstrans` — Label Translation
  - `mcstransd` daemon, `setrans.conf`, translating `s3:c10` to `ASIL-D`, NATO/URCSTS examples in repo, when to enable vs disable on ECU, repo: `mcstrans/share/examples/`

---

## Phase 11 — Debugging, Analysis & Forensics
*What goes wrong and how to investigate it*

- [ ] **11.1** — Reading AVC Denial Messages
  - Every field in an AVC log line dissected: `scontext`, `tcontext`, `tclass`, `permissive`, `pid`, `comm`, `path`, `dev`, `ino`, how to read them cold

- [ ] **11.2** — `audit2allow` & `audit2why`
  - Generating allow rules from denials, the danger of blindly applying audit2allow output, `audit2why` for human explanation, repo: `python/audit2allow/`

- [ ] **11.3** — `sesearch` & Policy Querying
  - `sesearch --allow -s ota_t`, finding what a domain can do, finding what can access a type, `seinfo -t`, `seinfo -r`, building a picture of policy coverage

- [ ] **11.4** — `apol` & `seinfo`
  - Policy analysis tools, domain transition graph, information flow analysis, finding policy gaps before an attacker does, repo: `python/sepolicy/`

- [ ] **11.5** — SELinux as an Intrusion Detection System
  - `auditallow` as a tripwire, `auditd` + `audisp-remote` for real-time streaming, detecting privilege escalation attempts via AVC patterns, SIEM integration, alerting on `unlabeled_t` access

- [ ] **11.6** — SELinux for Forensics
  - Post-incident: reading AVC logs to reconstruct attacker path, label evidence on files (xattr shows what type was accessed), timeline reconstruction, what SELinux logs prove vs what it cannot prove, log integrity with `chattr +a` and remote syslog

- [ ] **11.7** — Common Mistakes & Anti-Patterns
  - `unconfined_t` (the security black hole), `dontaudit` hiding real attacks, overly broad `allow` rules, forgetting to label new files, running in Permissive forever, copy-pasting from audit2allow without review

---

## Session Notes

*Use this section to record questions, things to revisit, and observations from each session.*

---

### Session 1 (Initial)
- Curriculum designed and plan approved
- Phase 0.1 taught: Kernel, syscalls, processes, file descriptors, daemons, hooks
- Phase 0.2 taught: Linux filesystem, file types, /proc, /sys, /dev, inodes, xattrs, squashfs, mounting
- Follow-up explanations: ptrace, bin_t, ioctl, log generation, log integrity
- Follow-up explanations: hooks, file descriptors, daemons (simple analogies)
- squashfs, mount, ls -l, ls -Z, restorecon, chattr, xattr, unlabeled_t, genfscon explained with analogies

---

*Add new session notes below this line:*

