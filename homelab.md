# Homelab — Infrastructure Overview

> Living document. Update as hardware and services change.

## Network

- **Subnet:** Private /24
- **Router:** TP-Link AXE75
- **Switch:** TP-Link SG108PE (managed, PoE)
- **UPS:** APC Back-UPS NS 1500M2 — protecting router and Proxmox nodes
- **Remote access:** Tailscale mesh VPN — all nodes reachable from anywhere

## Hardware

| Host | Hardware | RAM | Storage |
|---|---|---|---|
| pve1 | HP EliteDesk 800 G6 Mini (i7-10700T) | 32GB DDR4 | 512GB SSD |
| pve2 | HP EliteDesk 800 G6 Mini (i7-10700T) | 64GB DDR4 | 2TB NVMe (Samsung 990 Pro) |
| pve3 | Dell OptiPlex 7080 (i7-10700) | 64GB DDR4 | 1TB SSD |
| Mac Studio | Apple M4 Max | 64GB unified | 1TB |
| MacBook Pro | Apple M2 Pro | 96GB unified | 2TB |

## pve1

**Role:** Proxmox node — infrastructure

| Service | Details |
|---|---|
| Proxmox VE | Hypervisor |
| NUT (nut-server, nut-monitor) | UPS monitoring — primary, USB connected to APC BN1500M2 |
| Pi-hole | LXC (CT 110, 192.168.0.210) — DNS + ad blocking for entire network |

### VMs

| VM ID | Name | OS | Role |
|---|---|---|---|
| 100 | kali | Kali Linux | Attacker VM for security lab |

## pve2

**Role:** Proxmox node — services and targets

| Service | Details |
|---|---|
| Proxmox VE | Hypervisor |
| NUT (nut-monitor) | UPS monitoring — secondary client, monitors pve1 |

### VMs

| VM ID | Name | OS | Role |
|---|---|---|---|
| 101 | ubuntu-server | Ubuntu Server 24.04 | Application server |

### Services on ubuntu-server

| Service | Stack | Details |
|---|---|---|
| AI Email Triage | Python, FastAPI, PostgreSQL, Docker | Fetches Gmail, classifies with Ollama, sends HTML digest. Runs 5x daily at 08:00, 11:00, 14:00, 17:00, 20:00 ET |
| Pilot | FastAPI, React, PostgreSQL, Docker | Personal assistant platform — Tasks, Calendar, Career, School, Workout, Daily Briefing. Auto-deploys via cron from GHCR |
| Portainer | Docker | Container management UI |

## pve3

**Role:** Proxmox node — memory-intensive workloads, SIEM, testing VMs, services

| Service | Details |
|---|---|
| Proxmox VE | Hypervisor |
| NUT (nut-monitor) | UPS monitoring — secondary client, monitors pve1 |

### VMs

| VM ID | Name | OS | Role |
|---|---|---|---|
| 200 | wazuh | Ubuntu 22.04 | Wazuh SIEM — manager, indexer, dashboard via Docker Compose |

### Services on wazuh

| Service | Stack | Details |
|---|---|---|
| Wazuh | Docker Compose (manager + indexer + dashboard) | SIEM — agents on pve1, pve2, ubuntu-server |

### Wazuh Agent Coverage

| Host | Agent Name | Status |
|---|---|---|
| pve1 | pve1 | Active |
| pve2 | pve2 | Active |
| ubuntu-server | ubuntu-server | Active |

### Planned VM Layout

| VM | RAM | Disk | Purpose |
|---|---|---|---|
| wazuh (200) | 12GB | 200GB | SIEM |
| services | 16GB | 300GB | Docker/apps |
| lab pool | 28GB | 350GB | Testing VMs |
| host overhead | 4GB | 50GB | Proxmox |

## Mac Studio

**Role:** LLM inference server

| Service | Details |
|---|---|
| Ollama | Serves llama3:70b on port 11434. System daemon — starts at boot, no login required |
