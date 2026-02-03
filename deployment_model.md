# OT Security Platform Deployment Model (Virtual-First, Hardware-Ready)

## Goals
- Deliver full OT security functionality on-prem with no external connectivity required.
- Start with virtualized components to accelerate deployment and scale.
- Provide a clear, low-risk migration path to dedicated appliances when needed.
- Maintain consistent architecture, data flows, and operations across VM and hardware.

## Deployment Overview (On-Prem OT Environment)
**Core principle:** All components run inside the OT network boundary and can function fully offline. Optional outbound integrations (e.g., updates, threat intel) are additive and never required for core operation.

**Logical zones (example):**
- **OT Zone:** Industrial control network, sensors passively observe traffic.
- **Operations/Management Zone:** Analysis, storage, and management UI.
- **IT/DMZ (optional):** Controlled bridge for updates or reporting.

## Component Model (VM-Based)

### 1) Virtual Sensors (Passive Monitoring)
- **Purpose:** Capture OT traffic, decode protocols, and forward metadata/events to analysis.
- **Form factor:** VM deployed close to OT network segments (e.g., per cell/area).
- **Connectivity:** SPAN/TAP or virtual switch port mirroring; no inline changes.
- **Outputs:** Enriched telemetry and asset/activity metadata to analysis VM(s).

### 2) Analysis & Correlation
- **Purpose:** Normalize telemetry, correlate events, build asset inventory, detect anomalies.
- **Form factor:** Central VM or VM cluster in the management zone.
- **Data handling:** Stores time-series telemetry, alerts, asset inventory, and baselines.

### 3) Management UI & Admin Services
- **Purpose:** Operator UI, RBAC, configuration, reporting, policy workflows.
- **Form factor:** VM (can be co-located with analysis or separated for scale).

### 4) Update & Content Services (Optional, Offline-Capable)
- **Purpose:** Threat intel, protocol decoders, detection content.
- **Form factor:** VM with offline update packages or removable media import.
- **Connectivity:** Not required; updates can be staged and imported locally.

## Virtual-First Sizing & Scaling
- **Small site:** 1–2 sensors + single analysis/management VM.
- **Medium site:** Multiple sensors + dedicated analysis VM + dedicated UI VM.
- **Large site:** Sensor fleet + analysis cluster + HA management stack.

## Migration Path to Dedicated Hardware Appliances

### Phase 1: VM-Only Deployment
- Install sensors and analysis as VMs on existing hypervisor infrastructure.
- Establish baseline visibility and validate detections.

### Phase 2: Hybrid Deployment
- Replace the most performance-sensitive sensors with hardware appliances.
- Keep analysis and UI in VMs for operational flexibility.
- Ensure identical configuration artifacts and export/import workflows.

### Phase 3: Full Hardware (Optional)
- Migrate analysis and management VMs to appliance equivalents.
- Preserve the same logical topology and data flows.
- Maintain staged rollback by keeping VM snapshots or standby VMs.

**Migration enablers:**
- **Configuration portability:** JSON/YAML-based exports for sensor and analysis configs.
- **Data migration:** Export/import tools for asset inventory, baselines, and alerts.
- **Licensing parity:** Same licensing model across VM and hardware.
- **Operational continuity:** Rolling cutover with side-by-side validation.

## Offline-First Operation (No External Connectivity Required)
- All core functions (capture, detection, correlation, UI) operate without internet access.
- Optional update packages can be delivered via removable media or internal repositories.
- Time sync via on-prem NTP; alerts via local integrations (SIEM/syslog).
- Remote support is opt-in and can be disabled entirely.

## Example Deployment Blueprint

**OT Site A (Medium):**
- 4× Virtual Sensors (one per OT segment)
- 1× Analysis VM (8 vCPU, 32–64 GB RAM, local SSD)
- 1× UI/Management VM (4 vCPU, 16 GB RAM)
- Optional: 1× Update VM (offline content repository)

**Connectivity:**
- Sensors → Analysis: one-way telemetry (OT → Management zone)
- Analysis → UI: internal API
- No outbound internet required

## Operational Considerations
- **Security hardening:** Minimal services, allow-listed ports, signed packages.
- **Resilience:** VM snapshots, HA for analysis if required, local backups.
- **Compliance:** Data residency in OT environment; audit logs stored on-prem.
- **Performance:** Use hardware appliances when traffic volume exceeds VM capacity.

## Summary
This model delivers a virtual-first deployment for rapid on-prem OT visibility while preserving a straightforward migration path to dedicated hardware appliances. Core functionality remains fully operational without any external connectivity, ensuring resilience and compliance in constrained OT environments.
