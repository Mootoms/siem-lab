# Home SIEM Lab — Wazuh on Proxmox

This project documents my personal build of a home SIEM (Security Information and Event Management) lab.  
I’m using it to learn how real monitoring, detection, and response work from the ground up.  
I don’t have formal IT or security experience — this is me building it, breaking it, and fixing it until I understand how it all connects.

---

## Purpose

My goal is to move toward a blue-team or SOC role.  
This lab is where I’m teaching myself how logs, alerts, and detections actually function in practice instead of just reading about them.  
Everything here is built, configured, and maintained on my own hardware.

---

## Current Setup

- **Host:** Lenovo M720q running Proxmox VE  
- **SIEM:** Wazuh (manager and dashboard)  
- **Endpoints:** Windows and Linux VMs with Wazuh agents  
- **Attacker system:** Separate laptop used to simulate attacks and suspicious behavior  
- **Network:** Isolated virtual network to safely test detections and response steps

---

## Skills I’m Developing

- Deploying and maintaining a SIEM (Wazuh)
- Configuring agents and forwarding logs from multiple systems
- Reviewing alerts and tuning rules to reduce noise
- Creating detection tests using controlled attack tools
- Investigating and documenting security events

---

## Repository Structure

- `notes/` — daily or weekly build notes exported from Obsidian  
- `screenshots/` — setup and dashboard images  
- (more structured docs will be added as the project matures)

---

## Progress

| Stage | Status |
|--------|---------|
| Proxmox host install | Complete |
| Wazuh manager setup | Complete |
| Agent deployment | In progress |
| Test attack scenarios | Planned |
| Detection tuning | Planned |

---

## Why Share This

I’m making my process public to show consistency, not perfection.  
Each commit represents something I actually configured or tested.  
Commit frequency may vary — I often switch between this lab, other projects, online courses, and résumé improvements.  
All of it supports the same goal: building a practical foundation for a future blue-team role.

---

## Next Steps

- Build repeatable attack/detection playbooks  
- Document detection creation and tuning  
- Map tests to MITRE ATT&CK techniques  
- Continue publishing lab results and updates

---

**Live site:** [https://mootoms.github.io/siem-lab](https://mootoms.github.io/siem-lab)

