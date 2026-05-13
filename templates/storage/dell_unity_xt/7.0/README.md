# Dell EMC Unity by HTTP

Zabbix template for monitoring Dell EMC Unity storage arrays (XT 380/480/680/880, All-Flash, Hybrid) via Unisphere Management REST API.

## Overview

This template is designed for comprehensive monitoring of Dell EMC Unity storage systems through HTTP-based REST API calls to the Unisphere management interface. It provides real-time insights into system health, performance, capacity, and resource utilization across the entire storage infrastructure.

## Requirements

Zabbix version: 7.0 and higher.

## Tested versions

This template has been tested on:
- Dell EMC Unity OE 5.x
- Unity Array Models: XT 380, XT 480, XT 680, XT 880, All-Flash, Hybrid

## Configuration

> Zabbix should be configured according to the instructions in the [Templates out of the box](https://www.zabbix.com/documentation/7.0/manual/config/templates_out_of_the_box) section.

## Setup

### 1. Prerequisites

- Dell EMC Unity Unisphere Management interface must be accessible via HTTPS
- Read-only user credentials for the REST API
- Network connectivity from Zabbix agent/server to the Unisphere management IP/FQDN

### 2. Configure Macros

Edit the template or host and configure the following user macros (see table below for complete list).

### Macros used

|Name|Description|Default|
|----|-----------|-------|
|{$UNITY.URL}|<p>URL base de la Unisphere REST API (sin barra final).</p>|`https://unity-mgmt.example.com`|
|{$UNITY.USER}|<p>Usuario read-only de la Unisphere REST API.</p>|`monitor`|
|{$UNITY.PASSWORD}|<p>Contraseña del usuario.</p>|``|
|{$UNITY.HTTP.TIMEOUT}|<p>Timeout HTTP.</p>|`30s`|
|{$UNITY.POOL.PUSED.WARN}|<p>Umbral WARN (%) de uso de pool.</p>|`90`|
|{$UNITY.POOL.PUSED.HIGH}|<p>Umbral HIGH (%) de uso de pool.</p>|`95`|
|{$UNITY.POOL.PUSED.CRIT}|<p>Umbral CRIT (%) de uso de pool.</p>|`100`|
|{$UNITY.POOL.SUBSCRIBED.WARN}|<p>Umbral WARN (%) oversubscription pool.</p>|`150`|
|{$UNITY.FS.PUSED.WARN}|<p>Umbral WARN (%) de uso de filesystem.</p>|`90`|
|{$UNITY.FS.PUSED.HIGH}|<p>Umbral HIGH (%) de uso de filesystem.</p>|`95`|
|{$UNITY.LUN.PUSED.WARN}|<p>Umbral WARN (%) de uso de LUN thin.</p>|`90`|
|{$UNITY.SP.CPU.WARN}|<p>Umbral WARN (%) CPU SP.</p>|`80`|
|{$UNITY.SP.CPU.HIGH}|<p>Umbral HIGH (%) CPU SP.</p>|`90`|
|{$UNITY.LUN.LLD.MATCHES}|<p>Regex de nombres de LUN INCLUIDOS.</p>|`.*`|
|{$UNITY.LUN.LLD.NOT_MATCHES}|<p>Regex de nombres de LUN EXCLUIDOS.</p>|`CHANGE_IF_NEEDED`|
|{$UNITY.FS.LLD.MATCHES}|<p>Regex de nombres de FS INCLUIDOS.</p>|`.*`|
|{$UNITY.FS.LLD.NOT_MATCHES}|<p>Regex de nombres de FS EXCLUIDOS.</p>|`CHANGE_IF_NEEDED`|
|{$UNITY.POOL.LLD.MATCHES}|<p>Regex de nombres de pool INCLUIDOS.</p>|`.*`|
|{$UNITY.POOL.LLD.NOT_MATCHES}|<p>Regex de nombres de pool EXCLUIDOS.</p>|`CHANGE_IF_NEEDED`|
|{$UNITY.SNAP.AGE.WARN}|<p>Edad máx (s) sin nueva snap (default 7d).</p>|`604800`|
|{$UNITY.PERF.INTERVAL}|<p>Ventana (s) del realtime metric query.</p>|`60`|
|{$UNITY.FS.PUSED.CRIT}|<p>Filesystem used percentage threshold for DISASTER trigger.</p>|`100`|


### Items

|Name|Description|Type|Key and additional info|
|----|-----------|----|----------------------|
|Unity: Get system info|<p>Master item: system instance JSON.</p>|HTTP_AGENT|`unity.system.get`|
|Unity: System name|<p></p>|DEPENDENT|`unity.system.name`|
|Unity: Model|<p></p>|DEPENDENT|`unity.system.model`|
|Unity: Serial number|<p></p>|DEPENDENT|`unity.system.serial`|
|Unity: Platform|<p></p>|DEPENDENT|`unity.system.platform`|
|Unity: Health value|<p></p>|DEPENDENT|`unity.system.health.value`|
|Unity: Health description|<p></p>|DEPENDENT|`unity.system.health.desc`|
|Unity: Get software version|<p>Master item: returns raw JSON from Unisphere REST API.</p>|HTTP_AGENT|`unity.softver.get`|
|Unity: Software version|<p></p>|DEPENDENT|`unity.softver.version`|
|Unity: Full software version|<p></p>|DEPENDENT|`unity.softver.full`|
|Unity: Get active alerts|<p>Active (state=2) alerts.</p>|HTTP_AGENT|`unity.alerts.get`|
|Unity: Active alerts [Emergency]|<p>Número de alertas activas con severidad Emergency (0).</p>|DEPENDENT|`unity.alerts.count[0]`|
|Unity: Active alerts [Alert]|<p>Número de alertas activas con severidad Alert (1).</p>|DEPENDENT|`unity.alerts.count[1]`|
|Unity: Active alerts [Critical]|<p>Número de alertas activas con severidad Critical (2).</p>|DEPENDENT|`unity.alerts.count[2]`|
|Unity: Active alerts [Error]|<p>Número de alertas activas con severidad Error (3).</p>|DEPENDENT|`unity.alerts.count[3]`|
|Unity: Active alerts [Warning]|<p>Número de alertas activas con severidad Warning (4).</p>|DEPENDENT|`unity.alerts.count[4]`|
|Unity: Active alerts [Notice]|<p>Número de alertas activas con severidad Notice (5).</p>|DEPENDENT|`unity.alerts.count[5]`|
|Unity: Active alerts [Info]|<p>Número de alertas activas con severidad Info (6).</p>|DEPENDENT|`unity.alerts.count[6]`|
|Unity: Active alerts (raw JSON)|<p></p>|DEPENDENT|`unity.alerts.raw`|
|Unity: Get realtime performance|<p>Master script item: ejecuta un metricRealTimeQuery contra la cabina y devuelve el JSON con valore...</p>|SCRIPT|`unity.perf.get`|
|Unity perf: SP CPU utilization (avg)|<p></p>|DEPENDENT|`unity.perf.sp.cpu.avg`|
|Unity perf: SP CPU utilization (max)|<p></p>|DEPENDENT|`unity.perf.sp.cpu.max`|
|Unity perf: total IOPS|<p></p>|DEPENDENT|`unity.perf.iops.total`|
|Unity perf: read IOPS|<p></p>|DEPENDENT|`unity.perf.iops.read`|
|Unity perf: write IOPS|<p></p>|DEPENDENT|`unity.perf.iops.write`|
|Unity perf: total bandwidth (B/s)|<p></p>|DEPENDENT|`unity.perf.bw.total`|
|Unity perf: avg response time (ms)|<p></p>|DEPENDENT|`unity.perf.lun.latency`|
|Unity perf: net inBytesRate (Bps)|<p></p>|DEPENDENT|`unity.perf.net.in`|
|Unity perf: net outBytesRate (Bps)|<p></p>|DEPENDENT|`unity.perf.net.out`|


### Low-Level Discovery Rules

This template includes **18** discovery rules for automatic monitoring of dynamic resources:

|Name|Description|Type|Key and additional info|
|----|-----------|----|----------------------|
|Unity: Licenses discovery|<p></p><p>Creates **4** items and **3** triggers</p>|HTTP_AGENT|`unity.lld.licenses`|
|Unity: Storage Processors discovery|<p></p><p>Creates **6** items and **2** triggers</p>|HTTP_AGENT|`unity.lld.sp`|
|Unity: Disks discovery|<p></p><p>Creates **6** items and **1** triggers</p>|HTTP_AGENT|`unity.lld.disk`|
|Unity: DAE/Enclosure discovery|<p></p><p>Creates **2** items and **1** triggers</p>|HTTP_AGENT|`unity.lld.dae`|
|Unity: Power Supply discovery|<p></p><p>Creates **2** items and **1** triggers</p>|HTTP_AGENT|`unity.lld.powersupply`|
|Unity: Fan discovery|<p></p><p>Creates **2** items and **1** triggers</p>|HTTP_AGENT|`unity.lld.fan`|
|Unity: Battery discovery|<p></p><p>Creates **2** items and **1** triggers</p>|HTTP_AGENT|`unity.lld.battery`|
|Unity: IO Module discovery|<p></p><p>Creates **2** items and **1** triggers</p>|HTTP_AGENT|`unity.lld.iomodule`|
|Unity: Link Control Card discovery|<p></p><p>Creates **2** items and **1** triggers</p>|HTTP_AGENT|`unity.lld.lcc`|
|Unity: System Status Card discovery|<p></p><p>Creates **2** items and **1** triggers</p>|HTTP_AGENT|`unity.lld.ssc`|
|Unity: Internal SSD discovery|<p></p><p>Creates **2** items and **1** triggers</p>|HTTP_AGENT|`unity.lld.ssd`|
|Unity: Memory Module discovery|<p></p><p>Creates **2** items and **1** triggers</p>|HTTP_AGENT|`unity.lld.memorymodule`|
|Unity: Pools discovery|<p></p><p>Creates **12** items and **6** triggers</p>|HTTP_AGENT|`unity.lld.pool`|
|Unity: LUNs discovery|<p></p><p>Creates **8** items and **2** triggers</p>|HTTP_AGENT|`unity.lld.lun`|
|Unity: Filesystems discovery|<p></p><p>Creates **6** items and **5** triggers</p>|HTTP_AGENT|`unity.lld.fs`|
|Unity: NAS Servers discovery|<p></p><p>Creates **4** items and **3** triggers</p>|HTTP_AGENT|`unity.lld.nas`|
|Unity: Replication sessions discovery|<p></p><p>Creates **6** items and **3** triggers</p>|HTTP_AGENT|`unity.lld.repl`|
|Unity: Snapshots discovery (per storage resource)|<p></p><p>Creates **3** items and **1** triggers</p>|HTTP_AGENT|`unity.lld.snap`|


## Summary Statistics

- **Total Macros:** 22
- **Total Items:** 29
- **Total Discovery Rules (LLDs):** 18
- **Total Item Prototypes:** 73
- **Total Trigger Prototypes:** 35

## Monitored Components

### System Monitoring
- System health status and descriptions
- Software version information
- Platform and hardware identification
- Real-time performance metrics (CPU, IOPS, Bandwidth, Latency)
- Active alerts aggregation by severity

### Hardware Monitoring (Low-Level Discovery)
- **Storage Processors:** Health, model, memory, CPU utilization
- **Disks:** Health, capacity, model, tier type, technology
- **Enclosures (DAE):** Health and status
- **Power Supplies:** Health and operational status
- **Fans:** Health monitoring
- **Batteries:** Charge status and health
- **IO Modules:** Health and status
- **Link Control Cards:** Health monitoring
- **System Status Cards:** Health monitoring
- **Internal SSDs:** Health and capacity
- **Memory Modules:** Health monitoring

### Storage Resources (Low-Level Discovery)
- **Pools:** Capacity, usage %, subscription %, dedup/compression
- **LUNs:** Capacity, allocation, thin provisioning metrics
- **Filesystems:** Capacity, usage %, provisioning
- **NAS Servers:** Failover status, availability

### Replication & Data Protection
- **Replication Sessions:** Status, lag, synchronization state
- **Snapshots:** Age tracking, creation monitoring

## REST API Integration

The template communicates with the Unisphere REST API using:
- **Authentication:** HTTP Basic Auth + X-EMC-REST-CLIENT header
- **Data Format:** JSON
- **SSL/TLS:** Self-signed certificates supported (validation disabled)

## Data Collection Intervals

| Component | Interval | Purpose |
|-----------|----------|---------|
| System Info | 5m | Basic system health |
| Software Version | 1h | Version tracking |
| Alerts | 2m | Real-time alerting |
| Licenses | 1h | License monitoring |
| Hardware | 10-30m | Device health |
| Pools/LUNs/FS | 10m | Capacity tracking |
| Performance | 5m | Real-time metrics |

## Compatibility

- **Zabbix:** 7.0+
- **Unity OE:** 5.x+
- **Models:** XT 380/480/680/880, All-Flash, Hybrid

## Template Metadata

- **Vendor:** ECS
- **Version:** 7.0-1
- **Zabbix Version:** 7.0
- **Last Updated:** 2026-05-13

## License

MIT License - Free to use, modify, and distribute

---

For support and documentation, refer to Dell EMC Unisphere documentation and Zabbix official templates.
