# 🔐 Home SOC Lab | Security Operations Engineering
Four (4) years in security operations in an SEC-regulated environment. Hands-on work in a live cyber range. A stack of certs with more on the way.

And yet, here I am building a home lab. 😄

Definitely not to learn what Wazuh is. 

There's significant value in owning your own infrastructure. From designing the network to breaking something at 11 pm and figuring out why/how you broke it. There's a difference between this type of ownership and working within someone else's environment, and it shows in ways that are hard to fake.

My repo documents everything (network segmentation, hypervisor installation, SIEM deployment, etc.) including attack-and-defend exercises on intentionally vulnerable targets. In addition to the wins, I documented the failures, missteps, and reasoning behind every decision along the way.

Built to showcase. Documented to share. 🔧

## 🖥️ Hardware

| Device | Role | Key Specs |
|---|---|---|
| Dell Precision 7740 | Proxmox Hypervisor | Xeon E-2286M, 128GB RAM, VM dedicated 1TB NVMe SSD |
| Dell Precision 5530 | Management Workstation | Xeon E-2176M, 64GB RAM |
| MikroTik hEX (RB750Gr3) | Lab Gateway / Firewall | 5-port gigabit, RouterOS |
| MikroTik CSS610-8G-2S+IN | Managed Switch | 8x gigabit, 2x SFP+, SwOS, 802.1Q VLAN |
| Dell SFF (TBD) | AI Inference Node | Specs TBD — pending hardware audit |
| CyberPower CP1000PFCLCD | UPS | 1000VA/600W, Pure Sine Wave, AVR, 10 outlets |

---

## 🛡️ Zero Trust Design Principles

"Never trust, always verify" gets thrown around a lot. I actually mean it. 😤

Zero Trust isn't something you can buy. It's an approach to design that assumes everything (each user, each system, each application, etc.) is at risk of compromise until explicitly verified. This assumption shapes every decision in this build, from how VLANs are segmented to the administrative access controls.

- ✅ **Explicit verification** — Administrative access is only permitted from the management VLAN. No exceptions.
- ✅ **No any-to-any firewall rules** — Every inter-VLAN routing rule is explicit and intentional. If it is not explicitly allowed, it is denied.
- ✅ **Least privilege** — Accounts and services have only the access they need to function. Nothing more.
- ✅ **Separate admin and user accounts** — Administrative accounts are never used for day-to-day activity.
- ✅ **Victim VLAN isolation** — Systems on the victim VLAN cannot initiate connections to the management VLAN under any circumstances.
- ✅ **Attacker VLAN treated as hostile** — Traffic from the attacker VLAN is treated as untrusted by default at the firewall layer.
- ✅ **Victim systems considered potentially compromised** — Victim VLAN systems are assumed to be compromised at all times by design.
- ✅ **Universal logging** — Every VLAN feeds logs into the SIEM. Nothing goes unwatched.
- ✅ **Segmentation limits lateral movement** — Network segmentation ensures a compromised system cannot move freely across the lab environment.
- ✅ **Local AI inference** — LLM processing runs entirely on-premises via Ollama. No alert data, log content, or IOC details are transmitted to external AI APIs.
- 🔜 **MFA for Azure accounts** — Multi-factor authentication enforced on all Azure accounts. *(Phase 5)*
- 🔜 **Bastion/jump-host access** — Sensitive cloud systems accessible only through a dedicated jump host. *(Phase 5)*
- 🔜 **Least privilege in Azure** — RBAC enforced across all Azure resources with no standing admin access. *(Phase 5)*

If it is not documented, it did not happen. Every principle above maps to a concrete configuration decision somewhere in this repo. 🔒

## 🏗️ Architecture Overview

Running a security lab in the same network as everything else (your malware samples, attack tools, and intentionally vulnerable systems) is not a good idea. Everything about this lab was designed around that one constraint.

The MikroTik hEX Firewall and CSS610 managed switch enforce VLAN separation (using IEEE 802.1Q) among the five (5) isolated network segments. There are strict inter-VLAN routing rules that dictate which segment can communicate with which. 🔒
1. Management
2. Attacker
3. Victim
4. Security Tooling
5. AI/Automation
6. Fully Air-Gapped Sandbox

The virtualization layer runs on a Dell Precision 7740 mobile workstation with 128GB of RAM and a dedicated Gen 4 NVMe SSD, running Proxmox. I will take snapshots before running anything in my lab, allocate resources to each VM, and have a management workstation on its own dedicated VLAN with a direct uplink to the firewall. The entire lab can be a fire on top of a dumpster fire, and the management plane stays clean. 🔥

## 🗺️ Build Phases

This is a living project. Phases get checked off when they're done, not when I come up with them. 👷

- [ ] **[Phase 1: Network Foundation & Hypervisor Deployment](phase-1-network-foundation/README.md)** — 
VLAN segmentation, MikroTik configuration, and Proxmox installation on dedicated hardware.
- [ ] **Phase 2: Blue Team Stack** — Wazuh SIEM and Security Onion deployed, agents configured, and log ingestion verified.
- [ ] **Phase 3: Attack/Defend Range** — Kali Linux attacker VM against intentionally vulnerable targets with documented lab exercises.
- [ ] **Phase 4: Threat Intelligence Integration** — Live IOC feeds wired into the SIEM stack via AlienVault OTX, Abuse.ch, and MISP.
- [ ] **Phase 5: Hybrid Azure Extension** — On-premises lab extended into Azure via Site-to-Site VPN with cloud-native security tooling layered on top.
- [ ] **Phase 6: AI-Assisted SOC Automation** — Local LLM deployed via Ollama with agentic workflows automating Tier 1 alert triage, IOC enrichment, log correlation, and shift handoff reporting using LangChain, n8n, and the Wazuh and Security Onion APIs.

## 🛠️ Tools & Technologies

### 🌐 Network
| Tool | Description |
|---|---|
| ![MikroTik](https://img.shields.io/badge/MikroTik-RouterOS-blue) | Lab gateway, firewall, and inter-VLAN routing |
| ![MikroTik](https://img.shields.io/badge/MikroTik-SwOS-blue) | Managed switch VLAN enforcement via 802.1Q |

### 💻 Virtualization
| Tool | Description |
|---|---|
| ![Proxmox](https://img.shields.io/badge/Proxmox-VE-orange) | Hypervisor running all lab VMs on dedicated hardware |

### 🔒 Security Tools
| Tool | Description |
|---|---|
| ![Wazuh](https://img.shields.io/badge/Wazuh-SIEM-red) | Open source SIEM and EDR for log aggregation and alerting |
| ![Security Onion](https://img.shields.io/badge/Security%20Onion-NSM-green) | Network security monitoring and intrusion detection |
| ![Kali](https://img.shields.io/badge/Kali-Linux-557C94) | Dedicated attacker VM for lab exercises |

### 🤖 AI & Automation
| Tool | Description |
|---|---|
| ![Ollama](https://img.shields.io/badge/Ollama-Local%20LLM-black) | On-premises LLM inference — no data leaves the network |
| ![LangChain](https://img.shields.io/badge/LangChain-Agentic%20AI-green) | Agentic workflow orchestration for Tier 1 alert triage and indicators of compromise (IOC) enrichment |
| ![n8n](https://img.shields.io/badge/n8n-Workflow%20Automation-orange) | SOC playbook automation — triggers agentic workflows from Wazuh alerts |

### ☁️ Coming Soon
| Tool | Description |
|---|---|
| ![Azure](https://img.shields.io/badge/Microsoft-Azure-0078D4) | Site-to-Site VPN extension for hybrid lab architecture |
| ![MISP](https://img.shields.io/badge/MISP-Threat%20Intel-lightgrey) | Self-hosted threat intelligence platform |
| ![AlienVault](https://img.shields.io/badge/AlienVault-OTX-red) | Open threat exchange IOC feed integration |
| ![Abuse.ch](https://img.shields.io/badge/Abuse.ch-IOC%20Feeds-black) | Malware and C2 indicator feeds |

---

## 📡 Network Diagram

> 🚧 Diagrams coming in Phase 1. Check back once the hardware arrives and the network is live.

<!-- 
Once complete, replace this section with:

![Network Topology](phase-1-network-foundation/diagrams/vlan-topology.png)

![Port Map](phase-1-network-foundation/diagrams/port-map.png)

### VLAN Table

| VLAN ID | Name | Purpose | Internet Access |
|---|---|---|---|
| 10 | Management | Proxmox UI, switch management | Yes |
| 20 | Victim | Intentionally vulnerable target VMs | Restricted |
| 30 | Attacker | Kali Linux | Yes |
| 40 | Security Tools | Wazuh, Security Onion | Yes |
| 99 | Isolated Sandbox | Air-gapped malware analysis | No |

-->
