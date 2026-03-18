---
title: "Home Lab Overview - SOC Triage & Decision-Making Lab"
weight: 2
---

# SOC Triage & Decision-Making Home Lab 


## Purpose
This home lab exists to support the security projects documented on this site. It is intentionally scoped to generate realistic telemetry and enable repeatable investigation workflows.

The goal is to demonstrate how I:
- design a small environment that produces useful logs,
- build detections and triage logic in a SIEM,
- investigate alerts end-to-end,
- document findings and recommendations in a SOC-style format.

---
## Architecture

The lab produces endpoint + network events and forwards them to a centralized SIEM where detections, triage, and investigation take place.

![Home lab security architecture](/images/Homelab.svg)

**Data flow:**
1) Endpoint + firewall events are generated  
2) Logs are forwarded into Splunk  
3) Searches/alerts trigger on suspicious patterns  
4) I triage, pivot, validate, and document decisions

## Core Components

**OPNsense Firewall**  
Defines network boundaries and produces network-level telemetry used for traffic analysis.

**Proxmox Virtualization Host**  
Runs isolated virtual machines for 
- SIEM hosting
- "Victim" system endpoints
- "Attacker" system endpoints (for simulated attacks)

**Endpoints**  
Generate authentication activity, process execution, and system logs used during investigations.

**Splunk SIEM**  
Aggregates endpoint logs and telemetry to support alerting, pivot-based investigation, and timeline reconstruction.

---

## How This Lab Is Used
Each project uses this environment to simulate a realistic security investigation workflow:

1) **Generate** a controlled security scenario  
2) **Detect** it with a search/alert (thresholding, patterns, context)  
3) **Triage**: validate signal vs noise, establish scope, identify affected identity/host/IP  
4) **Investigate**: pivot across logs, correlate events, build a timeline  
5) **Recommend**: practical containment + hardening steps  
6) **Document**: write it up so another analyst could reproduce and learn from it

Each project references only the parts of the lab relevant to the scenario being demonstrated.

This lab is built to keep each project reproducible and easy to follow. 


























