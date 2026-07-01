# homelab-notes

Public documentation for my homelab infrastructure — sanitized for sharing (no IPs or credentials).

## Overview

A multi-node Proxmox cluster running self-hosted services, a SIEM, and an LLM inference server.

## Node Roles

| Node | Hardware | Role |
|---|---|---|
| pve1 | HP EliteDesk 800 G6 Mini (i7-10700T, 32GB) | Infrastructure — Pi-hole DNS, Kali VM |
| pve2 | HP EliteDesk 800 G6 Mini (i7-10700T, 64GB) | Applications — ubuntu-server VM |
| pve3 | Dell OptiPlex 7080 (i7-10700, 64GB) | SIEM — Wazuh |
| pve4 | Dell Precision 3240 Compact (i7 10th gen, 16GB) | NAS (pending HBA) |
| Mac Studio | Apple M4 Max, 64GB unified | LLM inference — Ollama |

## Services

| Service | Stack | Node |
|---|---|---|
| Pi-hole | LXC | pve1 |
| Wazuh SIEM | Docker Compose (manager + indexer + dashboard) | pve3 |
| Pilot (personal assistant) | FastAPI, React, PostgreSQL, Docker | ubuntu-server |
| AI Email Triage | Python, FastAPI, PostgreSQL, Docker | ubuntu-server |
| ML Gateway | FastAPI, Docker | ubuntu-server |
| Prometheus + Grafana | Docker | ubuntu-server |
| Ollama | llama3:70b, gemma4:12b, nomic-embed-text | Mac Studio |

## Related Repos

| Repo | Description |
|---|---|
| [ollama-inference-gateway](https://github.com/MarcosAOSperoni/ollama-inference-gateway) | ML gateway + Prometheus + Grafana stack |
| [pilot](https://github.com/MarcosAOSperoni/pilot) | Personal assistant platform |

## Files

- `homelab.md` — Sanitized infrastructure overview
