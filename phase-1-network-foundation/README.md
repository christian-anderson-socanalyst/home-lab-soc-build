# 🔧 Phase 1: Network Foundation & Hypervisor Deployment

> 🚧 This phase is currently in progress. 
> Content will be updated as the build progresses.

## Overview
<!-- 
Narrative intro explaining what Phase 1 accomplishes and why it has to come first. Coming soon.
-->

## Design Decisions
<!--
The most important section. Why MikroTik over other options, why this VLAN design, why the 5530 sits on the hEX directly, why Proxmox over VMware or Hyper-V. Coming soon.
-->

## VLAN Design
<!--
VLAN table and explanation of each segment. Coming soon.
-->

## Port Map
<!--
Physical port assignments for hEX and CSS610. Coming soon.
-->

## Phase Outcome
<!--
What a completed Phase 1 looks like. Coming soon.
-->

## 🟢 Startup Procedure

Follow this sequence every session. Skipping steps or powering things up out of order will cause headaches. Ask me how I know. 😄

1. **Power on UPS units** — confirm both units show clean input voltage on the LCD before proceeding. This assumes you have completely shut down everything in after your last session. More than likely the UPS units are still powered.
2. **Power on MikroTik CSS610** — allow 30 seconds to fully initialize
3. **Power on MikroTik hEX** — allow 60 seconds for RouterOS to fully boot and establish routing
4. **Power on Dell Precision 7740** — wait for Proxmox to fully boot before touching anything else
5. **Power on Dell Precision 5530** — open browser and confirm Proxmox web UI is accessible at its management IP address
6. **Start VMs as needed** — never start VMs until Proxmox UI is fully accessible and responsive

---

## 🔴 Shutdown Procedure

Shutdown is the Startup Procedure in reverse. 🔒

1. **Shutdown all running VMs** — use the Proxmox web UI to shutdown each VM. Never force stop a VM unless absolutely necessary.
2. **Shutdown Proxmox** — from the web UI select the node and choose shutdown. Wait for the 7740 to fully power off before proceeding.
3. **Power off Dell Precision 5530**
4. **Power off MikroTik hEX**
5. **Power off MikroTik CSS610** — networking stack always powers down last.
6. **UPS units** — remain powered as long as anything is plugged in. Power these off last if shutting down completely.

---

## ⚠️ Why Order Matters

- Proxmox writes the state of each virtual machine (VM) to the disk continuously. If you were to lose power while a VM's state is being written to the disk, it could destroy the VM image. So, always completely shutdown your VMs before powering off your host.
- The networking stack loads first because Proxmox requires an available network path during initialization so that the network stack management interface loads properly
- The management workstation loads last since there is no need for it to exist until the hypervisor has finished loading
- Always take a snapshot (of each VM) prior to each session, so in a worst case scenaio you have to restore everything. Way better than starting from scratch. 📸

## Files In This Section

| File | Description |
|---|---|
| [hex-config.md](hex-config.md) | MikroTik hEX initial setup and firewall rules |
| [css610-config.md](css610-config.md) | CSS610 VLAN and port configuration |
| [proxmox-install.md](proxmox-install.md) | Proxmox installation and post-install hardening |
| [lessons-learned.md](lessons-learned.md) | What went wrong and how it was resolved |

