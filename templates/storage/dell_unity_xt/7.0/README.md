# Dell EMC Unity by HTTP

Zabbix template for monitoring Dell EMC Unity storage arrays (XT 380/480/680/880, All-Flash, Hybrid) via Unisphere Management REST API.

## Overview

This template is designed for the effortless deployment of Dell EMC Unity monitoring by Zabbix via HTTP Agent and doesn't require any external scripts.

## Requirements

Zabbix version: 7.0 and higher.

## Tested versions

This template has been tested on:
- Dell EMC Unity, OE version 5.x
- Models: XT 380, XT 480, XT 680, XT 880, All-Flash, Hybrid

## Configuration

> Zabbix should be configured according to the instructions in the [Templates out of the box](https://www.zabbix.com/documentation/7.0/manual/config/templates_out_of_the_box) section.

## Setup

1. Create a monitoring user on Unisphere with read-only API access.

2. Set the Unisphere management URL in the host macro `{$UNITY.URL}` as `https://<management-ip-or-fqdn>` (without trailing slash).

3. Set the username and password in the host macros `{$UNITY.USER}` and `{$UNITY.PASSWORD}`.

4. Ensure network connectivity from the Zabbix server/agent to the Unisphere management interface over HTTPS (port 443).

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
|----|-----------|----|----|
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
|Unity: Get realtime performance|<p>Master script item: ejecuta un metricRealTimeQuery contra...</p>|SCRIPT|`unity.perf.get`|
|Unity perf: SP CPU utilization (avg)|<p></p>|DEPENDENT|`unity.perf.sp.cpu.avg`|
|Unity perf: SP CPU utilization (max)|<p></p>|DEPENDENT|`unity.perf.sp.cpu.max`|
|Unity perf: total IOPS|<p></p>|DEPENDENT|`unity.perf.iops.total`|
|Unity perf: read IOPS|<p></p>|DEPENDENT|`unity.perf.iops.read`|
|Unity perf: write IOPS|<p></p>|DEPENDENT|`unity.perf.iops.write`|
|Unity perf: total bandwidth (B/s)|<p></p>|DEPENDENT|`unity.perf.bw.total`|
|Unity perf: avg response time (ms)|<p></p>|DEPENDENT|`unity.perf.lun.latency`|
|Unity perf: net inBytesRate (Bps)|<p></p>|DEPENDENT|`unity.perf.net.in`|
|Unity perf: net outBytesRate (Bps)|<p></p>|DEPENDENT|`unity.perf.net.out`|


### LLD rule Unity: Licenses discovery

|Name|Description|Type|Key and additional info|
|----|-----------|----|----|
|Unity: Licenses discovery|<p></p>|HTTP_AGENT|`unity.lld.licenses`|

### Item prototypes for Unity: Licenses discovery

|Name|Description|Type|Key and additional info|
|----|-----------|----|----|
|Unity license [{#LIC.NAME}]: get|<p>Master item: returns raw JSON from Unisphere REST API.</p>|HTTP_AGENT|`unity.license.get[{#LIC.ID}]`|
|Unity license [{#LIC.NAME}]: installed|<p></p>|DEPENDENT|`unity.license.installed[{#LIC.ID}]`|
|Unity license [{#LIC.NAME}]: valid|<p></p>|DEPENDENT|`unity.license.valid[{#LIC.ID}]`|
|Unity license [{#LIC.NAME}]: expires (epoch)|<p></p>|DEPENDENT|`unity.license.expires[{#LIC.ID}]`|


### Trigger prototypes for Unity: Licenses discovery

|Name|Description|Expression|Severity|
|----|-----------|----------|--------|
|Unity license [{#LIC.NAME}]: not valid|<p></p>|`last(/Dell EMC Unity by HTTP/unity.license.valid[{#LIC.ID}])=0`|WARNING|
|Unity license [{#LIC.NAME}]: expires in <30 days|<p></p>|`last(/Dell EMC Unity by HTTP/unity.license.expires[{#LIC.ID}])>0 and (last(/Dell EMC Unity by HTTP/unity.license.expires[{#LIC.ID}])-now())<2592000 and (last(/Dell EMC Unity by HTTP/unity.license.expires[{#LIC.ID}])-now())>0`|WARNING|
|Unity license [{#LIC.NAME}]: expired|<p></p>|`last(/Dell EMC Unity by HTTP/unity.license.expires[{#LIC.ID}])>0 and (last(/Dell EMC Unity by HTTP/unity.license.expires[{#LIC.ID}])-now())<0`|WARNING|


### LLD rule Unity: Storage Processors discovery

|Name|Description|Type|Key and additional info|
|----|-----------|----|----|
|Unity: Storage Processors discovery|<p></p>|HTTP_AGENT|`unity.lld.sp`|

### Item prototypes for Unity: Storage Processors discovery

|Name|Description|Type|Key and additional info|
|----|-----------|----|----|
|Unity SP [{#SP.NAME}]: get|<p>Master item: returns raw JSON from Unisphere REST API.</p>|HTTP_AGENT|`unity.sp.get[{#SP.ID}]`|
|Unity SP [{#SP.NAME}]: health value|<p></p>|DEPENDENT|`unity.sp.health[{#SP.ID}]`|
|Unity SP [{#SP.NAME}]: health description|<p></p>|DEPENDENT|`unity.sp.healthdesc[{#SP.ID}]`|
|Unity SP [{#SP.NAME}]: memory size|<p></p>|DEPENDENT|`unity.sp.memory[{#SP.ID}]`|
|Unity SP [{#SP.NAME}]: model|<p></p>|DEPENDENT|`unity.sp.model[{#SP.ID}]`|
|Unity SP [{#SP.NAME}]: rescue mode|<p></p>|DEPENDENT|`unity.sp.rescue[{#SP.ID}]`|


### Trigger prototypes for Unity: Storage Processors discovery

|Name|Description|Expression|Severity|
|----|-----------|----------|--------|
|Unity SP [{#SP.NAME}]: health not OK|<p></p>|`last(/Dell EMC Unity by HTTP/unity.sp.health[{#SP.ID}])>5 and last(/Dell EMC Unity by HTTP/unity.sp.health[{#SP.ID}])<>7`|HIGH|
|Unity SP [{#SP.NAME}]: in rescue mode|<p></p>|`last(/Dell EMC Unity by HTTP/unity.sp.rescue[{#SP.ID}])=1`|HIGH|


### LLD rule Unity: Disks discovery

|Name|Description|Type|Key and additional info|
|----|-----------|----|----|
|Unity: Disks discovery|<p></p>|HTTP_AGENT|`unity.lld.disk`|

### Item prototypes for Unity: Disks discovery

|Name|Description|Type|Key and additional info|
|----|-----------|----|----|
|Unity disk [{#DISK.NAME}]: get|<p>Master item: returns raw JSON from Unisphere REST API.</p>|HTTP_AGENT|`unity.disk.get[{#DISK.ID}]`|
|Unity disk [{#DISK.NAME}]: health value|<p></p>|DEPENDENT|`unity.disk.health[{#DISK.ID}]`|
|Unity disk [{#DISK.NAME}]: size|<p></p>|DEPENDENT|`unity.disk.size[{#DISK.ID}]`|
|Unity disk [{#DISK.NAME}]: model|<p></p>|DEPENDENT|`unity.disk.model[{#DISK.ID}]`|
|Unity disk [{#DISK.NAME}]: tier type|<p></p>|DEPENDENT|`unity.disk.tier[{#DISK.ID}]`|
|Unity disk [{#DISK.NAME}]: technology|<p></p>|DEPENDENT|`unity.disk.tech[{#DISK.ID}]`|


### Trigger prototypes for Unity: Disks discovery

|Name|Description|Expression|Severity|
|----|-----------|----------|--------|
|Unity disk [{#DISK.NAME}]: health not OK|<p></p>|`last(/Dell EMC Unity by HTTP/unity.disk.health[{#DISK.ID}])>5 and last(/Dell EMC Unity by HTTP/unity.disk.health[{#DISK.ID}])<>7`|WARNING|


### LLD rule Unity: DAE/Enclosure discovery

|Name|Description|Type|Key and additional info|
|----|-----------|----|----|
|Unity: DAE/Enclosure discovery|<p></p>|HTTP_AGENT|`unity.lld.dae`|

### Item prototypes for Unity: DAE/Enclosure discovery

|Name|Description|Type|Key and additional info|
|----|-----------|----|----|
|Unity DAE/Enclosure [{#DAE.NAME}]: get|<p>Master item: returns raw JSON from Unisphere REST API.</p>|HTTP_AGENT|`unity.dae.get[{#DAE.ID}]`|
|Unity DAE/Enclosure [{#DAE.NAME}]: health value|<p></p>|DEPENDENT|`unity.dae.health[{#DAE.ID}]`|


### Trigger prototypes for Unity: DAE/Enclosure discovery

|Name|Description|Expression|Severity|
|----|-----------|----------|--------|
|Unity DAE/Enclosure [{#DAE.NAME}]: health not OK|<p></p>|`last(/Dell EMC Unity by HTTP/unity.dae.health[{#DAE.ID}])>5 and last(/Dell EMC Unity by HTTP/unity.dae.health[{#DAE.ID}])<>7`|HIGH|


### LLD rule Unity: Power Supply discovery

|Name|Description|Type|Key and additional info|
|----|-----------|----|----|
|Unity: Power Supply discovery|<p></p>|HTTP_AGENT|`unity.lld.powersupply`|

### Item prototypes for Unity: Power Supply discovery

|Name|Description|Type|Key and additional info|
|----|-----------|----|----|
|Unity Power Supply [{#POWERSUPPLY.NAME}]: get|<p>Master item: returns raw JSON from Unisphere REST API.</p>|HTTP_AGENT|`unity.powersupply.get[{#POWERSUPPLY.ID}]`|
|Unity Power Supply [{#POWERSUPPLY.NAME}]: health value|<p></p>|DEPENDENT|`unity.powersupply.health[{#POWERSUPPLY.ID}]`|


### Trigger prototypes for Unity: Power Supply discovery

|Name|Description|Expression|Severity|
|----|-----------|----------|--------|
|Unity Power Supply [{#POWERSUPPLY.NAME}]: health not OK|<p></p>|`last(/Dell EMC Unity by HTTP/unity.powersupply.health[{#POWERSUPPLY.ID}])>5 and last(/Dell EMC Unity by HTTP/unity.powersupply.health[{#POWERSUPPLY.ID}])<>7`|WARNING|


### LLD rule Unity: Fan discovery

|Name|Description|Type|Key and additional info|
|----|-----------|----|----|
|Unity: Fan discovery|<p></p>|HTTP_AGENT|`unity.lld.fan`|

### Item prototypes for Unity: Fan discovery

|Name|Description|Type|Key and additional info|
|----|-----------|----|----|
|Unity Fan [{#FAN.NAME}]: get|<p>Master item: returns raw JSON from Unisphere REST API.</p>|HTTP_AGENT|`unity.fan.get[{#FAN.ID}]`|
|Unity Fan [{#FAN.NAME}]: health value|<p></p>|DEPENDENT|`unity.fan.health[{#FAN.ID}]`|


### Trigger prototypes for Unity: Fan discovery

|Name|Description|Expression|Severity|
|----|-----------|----------|--------|
|Unity Fan [{#FAN.NAME}]: health not OK|<p></p>|`last(/Dell EMC Unity by HTTP/unity.fan.health[{#FAN.ID}])>5 and last(/Dell EMC Unity by HTTP/unity.fan.health[{#FAN.ID}])<>7`|WARNING|


### LLD rule Unity: Battery discovery

|Name|Description|Type|Key and additional info|
|----|-----------|----|----|
|Unity: Battery discovery|<p></p>|HTTP_AGENT|`unity.lld.battery`|

### Item prototypes for Unity: Battery discovery

|Name|Description|Type|Key and additional info|
|----|-----------|----|----|
|Unity Battery [{#BATTERY.NAME}]: get|<p>Master item: returns raw JSON from Unisphere REST API.</p>|HTTP_AGENT|`unity.battery.get[{#BATTERY.ID}]`|
|Unity Battery [{#BATTERY.NAME}]: health value|<p></p>|DEPENDENT|`unity.battery.health[{#BATTERY.ID}]`|


### Trigger prototypes for Unity: Battery discovery

|Name|Description|Expression|Severity|
|----|-----------|----------|--------|
|Unity Battery [{#BATTERY.NAME}]: health not OK|<p></p>|`last(/Dell EMC Unity by HTTP/unity.battery.health[{#BATTERY.ID}])>5 and last(/Dell EMC Unity by HTTP/unity.battery.health[{#BATTERY.ID}])<>7`|WARNING|


### LLD rule Unity: IO Module discovery

|Name|Description|Type|Key and additional info|
|----|-----------|----|----|
|Unity: IO Module discovery|<p></p>|HTTP_AGENT|`unity.lld.iomodule`|

### Item prototypes for Unity: IO Module discovery

|Name|Description|Type|Key and additional info|
|----|-----------|----|----|
|Unity IO Module [{#IOMODULE.NAME}]: get|<p>Master item: returns raw JSON from Unisphere REST API.</p>|HTTP_AGENT|`unity.iomodule.get[{#IOMODULE.ID}]`|
|Unity IO Module [{#IOMODULE.NAME}]: health value|<p></p>|DEPENDENT|`unity.iomodule.health[{#IOMODULE.ID}]`|


### Trigger prototypes for Unity: IO Module discovery

|Name|Description|Expression|Severity|
|----|-----------|----------|--------|
|Unity IO Module [{#IOMODULE.NAME}]: health not OK|<p></p>|`last(/Dell EMC Unity by HTTP/unity.iomodule.health[{#IOMODULE.ID}])>5 and last(/Dell EMC Unity by HTTP/unity.iomodule.health[{#IOMODULE.ID}])<>7`|HIGH|


### LLD rule Unity: Link Control Card discovery

|Name|Description|Type|Key and additional info|
|----|-----------|----|----|
|Unity: Link Control Card discovery|<p></p>|HTTP_AGENT|`unity.lld.lcc`|

### Item prototypes for Unity: Link Control Card discovery

|Name|Description|Type|Key and additional info|
|----|-----------|----|----|
|Unity Link Control Card [{#LCC.NAME}]: get|<p>Master item: returns raw JSON from Unisphere REST API.</p>|HTTP_AGENT|`unity.lcc.get[{#LCC.ID}]`|
|Unity Link Control Card [{#LCC.NAME}]: health value|<p></p>|DEPENDENT|`unity.lcc.health[{#LCC.ID}]`|


### Trigger prototypes for Unity: Link Control Card discovery

|Name|Description|Expression|Severity|
|----|-----------|----------|--------|
|Unity Link Control Card [{#LCC.NAME}]: health not OK|<p></p>|`last(/Dell EMC Unity by HTTP/unity.lcc.health[{#LCC.ID}])>5 and last(/Dell EMC Unity by HTTP/unity.lcc.health[{#LCC.ID}])<>7`|WARNING|


### LLD rule Unity: System Status Card discovery

|Name|Description|Type|Key and additional info|
|----|-----------|----|----|
|Unity: System Status Card discovery|<p></p>|HTTP_AGENT|`unity.lld.ssc`|

### Item prototypes for Unity: System Status Card discovery

|Name|Description|Type|Key and additional info|
|----|-----------|----|----|
|Unity System Status Card [{#SSC.NAME}]: get|<p>Master item: returns raw JSON from Unisphere REST API.</p>|HTTP_AGENT|`unity.ssc.get[{#SSC.ID}]`|
|Unity System Status Card [{#SSC.NAME}]: health value|<p></p>|DEPENDENT|`unity.ssc.health[{#SSC.ID}]`|


### Trigger prototypes for Unity: System Status Card discovery

|Name|Description|Expression|Severity|
|----|-----------|----------|--------|
|Unity System Status Card [{#SSC.NAME}]: health not OK|<p></p>|`last(/Dell EMC Unity by HTTP/unity.ssc.health[{#SSC.ID}])>5 and last(/Dell EMC Unity by HTTP/unity.ssc.health[{#SSC.ID}])<>7`|WARNING|


### LLD rule Unity: Internal SSD discovery

|Name|Description|Type|Key and additional info|
|----|-----------|----|----|
|Unity: Internal SSD discovery|<p></p>|HTTP_AGENT|`unity.lld.ssd`|

### Item prototypes for Unity: Internal SSD discovery

|Name|Description|Type|Key and additional info|
|----|-----------|----|----|
|Unity Internal SSD [{#SSD.NAME}]: get|<p>Master item: returns raw JSON from Unisphere REST API.</p>|HTTP_AGENT|`unity.ssd.get[{#SSD.ID}]`|
|Unity Internal SSD [{#SSD.NAME}]: health value|<p></p>|DEPENDENT|`unity.ssd.health[{#SSD.ID}]`|


### Trigger prototypes for Unity: Internal SSD discovery

|Name|Description|Expression|Severity|
|----|-----------|----------|--------|
|Unity Internal SSD [{#SSD.NAME}]: health not OK|<p></p>|`last(/Dell EMC Unity by HTTP/unity.ssd.health[{#SSD.ID}])>5 and last(/Dell EMC Unity by HTTP/unity.ssd.health[{#SSD.ID}])<>7`|WARNING|


### LLD rule Unity: Memory Module discovery

|Name|Description|Type|Key and additional info|
|----|-----------|----|----|
|Unity: Memory Module discovery|<p></p>|HTTP_AGENT|`unity.lld.memorymodule`|

### Item prototypes for Unity: Memory Module discovery

|Name|Description|Type|Key and additional info|
|----|-----------|----|----|
|Unity Memory Module [{#MEMORYMODULE.NAME}]: get|<p>Master item: returns raw JSON from Unisphere REST API.</p>|HTTP_AGENT|`unity.memorymodule.get[{#MEMORYMODULE.ID}]`|
|Unity Memory Module [{#MEMORYMODULE.NAME}]: health value|<p></p>|DEPENDENT|`unity.memorymodule.health[{#MEMORYMODULE.ID}]`|


### Trigger prototypes for Unity: Memory Module discovery

|Name|Description|Expression|Severity|
|----|-----------|----------|--------|
|Unity Memory Module [{#MEMORYMODULE.NAME}]: health not OK|<p></p>|`last(/Dell EMC Unity by HTTP/unity.memorymodule.health[{#MEMORYMODULE.ID}])>5 and last(/Dell EMC Unity by HTTP/unity.memorymodule.health[{#MEMORYMODULE.ID}])<>7`|HIGH|


### LLD rule Unity: Pools discovery

|Name|Description|Type|Key and additional info|
|----|-----------|----|----|
|Unity: Pools discovery|<p></p>|HTTP_AGENT|`unity.lld.pool`|

### Item prototypes for Unity: Pools discovery

|Name|Description|Type|Key and additional info|
|----|-----------|----|----|
|Unity pool [{#POOL.NAME}]: get|<p>Master item: returns raw JSON from Unisphere REST API.</p>|HTTP_AGENT|`unity.pool.get[{#POOL.ID}]`|
|Unity pool [{#POOL.NAME}]: health value|<p></p>|DEPENDENT|`unity.pool.health[{#POOL.ID}]`|
|Unity pool [{#POOL.NAME}]: total size|<p></p>|DEPENDENT|`unity.pool.size.total[{#POOL.ID}]`|
|Unity pool [{#POOL.NAME}]: used size|<p></p>|DEPENDENT|`unity.pool.size.used[{#POOL.ID}]`|
|Unity pool [{#POOL.NAME}]: free size|<p></p>|DEPENDENT|`unity.pool.size.free[{#POOL.ID}]`|
|Unity pool [{#POOL.NAME}]: subscribed|<p></p>|DEPENDENT|`unity.pool.size.subs[{#POOL.ID}]`|
|Unity pool [{#POOL.NAME}]: preallocated|<p></p>|DEPENDENT|`unity.pool.size.prealloc[{#POOL.ID}]`|
|Unity pool [{#POOL.NAME}]: data reduction ratio|<p></p>|DEPENDENT|`unity.pool.dr.ratio[{#POOL.ID}]`|
|Unity pool [{#POOL.NAME}]: data reduction percent|<p></p>|DEPENDENT|`unity.pool.dr.percent[{#POOL.ID}]`|
|Unity pool [{#POOL.NAME}]: data reduction saved|<p></p>|DEPENDENT|`unity.pool.dr.saved[{#POOL.ID}]`|
|Unity pool [{#POOL.NAME}]: used %|<p></p>|CALCULATED|`unity.pool.pused[{#POOL.ID}]`|
|Unity pool [{#POOL.NAME}]: subscription %|<p></p>|CALCULATED|`unity.pool.psubs[{#POOL.ID}]`|


### Trigger prototypes for Unity: Pools discovery

|Name|Description|Expression|Severity|
|----|-----------|----------|--------|
|Unity pool [{#POOL.NAME}]: health not OK|<p></p>|`last(/Dell EMC Unity by HTTP/unity.pool.health[{#POOL.ID}])>5 and last(/Dell EMC Unity by HTTP/unity.pool.health[{#POOL.ID}])<>7 and last(/Dell EMC Unity by HTTP/unity.pool.health[{#POOL.ID}])<25`|HIGH|
|Unity pool [{#POOL.NAME}]: usage above {$UNITY.POOL.PUSED.WARN}%|<p></p>|`last(/Dell EMC Unity by HTTP/unity.pool.pused[{#POOL.ID}])>{$UNITY.POOL.PUSED.WARN}`|WARNING|
|Unity pool [{#POOL.NAME}]: usage above {$UNITY.POOL.PUSED.HIGH}%|<p></p>|`last(/Dell EMC Unity by HTTP/unity.pool.pused[{#POOL.ID}])>{$UNITY.POOL.PUSED.HIGH}`|HIGH|
|Unity pool [{#POOL.NAME}]: usage above {$UNITY.POOL.PUSED.CRIT}%|<p></p>|`last(/Dell EMC Unity by HTTP/unity.pool.pused[{#POOL.ID}])>{$UNITY.POOL.PUSED.CRIT}`|DISASTER|
|Unity pool [{#POOL.NAME}]: oversubscribed > {$UNITY.POOL.SUBSCRIBED.WARN}%|<p></p>|`last(/Dell EMC Unity by HTTP/unity.pool.psubs[{#POOL.ID}])>{$UNITY.POOL.SUBSCRIBED.WARN}`|WARNING|
|Unity pool [{#POOL.NAME}]: health critical (offline/non-recoverable)|<p>Pool offline / non-recoverable.</p>|`last(/Dell EMC Unity by HTTP/unity.pool.health[{#POOL.ID}])>=25`|DISASTER|


### LLD rule Unity: LUNs discovery

|Name|Description|Type|Key and additional info|
|----|-----------|----|----|
|Unity: LUNs discovery|<p></p>|HTTP_AGENT|`unity.lld.lun`|

### Item prototypes for Unity: LUNs discovery

|Name|Description|Type|Key and additional info|
|----|-----------|----|----|
|Unity LUN [{#LUN.NAME}]: get|<p>Master item: returns raw JSON from Unisphere REST API.</p>|HTTP_AGENT|`unity.lun.get[{#LUN.ID}]`|
|Unity LUN [{#LUN.NAME}]: health value|<p></p>|DEPENDENT|`unity.lun.health[{#LUN.ID}]`|
|Unity LUN [{#LUN.NAME}]: total size|<p></p>|DEPENDENT|`unity.lun.size.total[{#LUN.ID}]`|
|Unity LUN [{#LUN.NAME}]: allocated size|<p></p>|DEPENDENT|`unity.lun.size.alloc[{#LUN.ID}]`|
|Unity LUN [{#LUN.NAME}]: host-visible used|<p></p>|DEPENDENT|`unity.lun.size.used[{#LUN.ID}]`|
|Unity LUN [{#LUN.NAME}]: thin|<p></p>|DEPENDENT|`unity.lun.thin[{#LUN.ID}]`|
|Unity LUN [{#LUN.NAME}]: data reduction|<p></p>|DEPENDENT|`unity.lun.comp[{#LUN.ID}]`|
|Unity LUN [{#LUN.NAME}]: allocated %|<p></p>|CALCULATED|`unity.lun.palloc[{#LUN.ID}]`|


### Trigger prototypes for Unity: LUNs discovery

|Name|Description|Expression|Severity|
|----|-----------|----------|--------|
|Unity LUN [{#LUN.NAME}]: health not OK|<p></p>|`last(/Dell EMC Unity by HTTP/unity.lun.health[{#LUN.ID}])>5 and last(/Dell EMC Unity by HTTP/unity.lun.health[{#LUN.ID}])<>7`|WARNING|
|Unity LUN [{#LUN.NAME}]: allocated >{$UNITY.LUN.PUSED.WARN}%|<p></p>|`last(/Dell EMC Unity by HTTP/unity.lun.palloc[{#LUN.ID}])>{$UNITY.LUN.PUSED.WARN}`|WARNING|


### LLD rule Unity: Filesystems discovery

|Name|Description|Type|Key and additional info|
|----|-----------|----|----|
|Unity: Filesystems discovery|<p></p>|HTTP_AGENT|`unity.lld.fs`|

### Item prototypes for Unity: Filesystems discovery

|Name|Description|Type|Key and additional info|
|----|-----------|----|----|
|Unity FS [{#FS.NAME}]: get|<p>Master item: returns raw JSON from Unisphere REST API.</p>|HTTP_AGENT|`unity.fs.get[{#FS.ID}]`|
|Unity FS [{#FS.NAME}]: health value|<p></p>|DEPENDENT|`unity.fs.health[{#FS.ID}]`|
|Unity FS [{#FS.NAME}]: total size|<p></p>|DEPENDENT|`unity.fs.size.total[{#FS.ID}]`|
|Unity FS [{#FS.NAME}]: used size|<p></p>|DEPENDENT|`unity.fs.size.used[{#FS.ID}]`|
|Unity FS [{#FS.NAME}]: allocated size|<p></p>|DEPENDENT|`unity.fs.size.alloc[{#FS.ID}]`|
|Unity FS [{#FS.NAME}]: used %|<p></p>|CALCULATED|`unity.fs.pused[{#FS.ID}]`|


### Trigger prototypes for Unity: Filesystems discovery

|Name|Description|Expression|Severity|
|----|-----------|----------|--------|
|Unity FS [{#FS.NAME}]: health not OK|<p></p>|`last(/Dell EMC Unity by HTTP/unity.fs.health[{#FS.ID}])>5 and last(/Dell EMC Unity by HTTP/unity.fs.health[{#FS.ID}])<>7 and last(/Dell EMC Unity by HTTP/unity.fs.health[{#FS.ID}])<25`|HIGH|
|Unity FS [{#FS.NAME}]: used >{$UNITY.FS.PUSED.WARN}%|<p></p>|`last(/Dell EMC Unity by HTTP/unity.fs.pused[{#FS.ID}])>{$UNITY.FS.PUSED.WARN}`|WARNING|
|Unity FS [{#FS.NAME}]: used >{$UNITY.FS.PUSED.HIGH}%|<p></p>|`last(/Dell EMC Unity by HTTP/unity.fs.pused[{#FS.ID}])>{$UNITY.FS.PUSED.HIGH}`|HIGH|
|Unity FS [{#FS.NAME}]: used >{$UNITY.FS.PUSED.CRIT}%|<p>Filesystem at or above CRIT threshold (full / c...</p>|`last(/Dell EMC Unity by HTTP/unity.fs.pused[{#FS.ID}])>{$UNITY.FS.PUSED.CRIT}`|DISASTER|
|Unity FS [{#FS.NAME}]: health critical (offline/non-recoverable)|<p>Filesystem offline / share inaccessible.</p>|`last(/Dell EMC Unity by HTTP/unity.fs.health[{#FS.ID}])>=25`|DISASTER|


### LLD rule Unity: NAS Servers discovery

|Name|Description|Type|Key and additional info|
|----|-----------|----|----|
|Unity: NAS Servers discovery|<p></p>|HTTP_AGENT|`unity.lld.nas`|

### Item prototypes for Unity: NAS Servers discovery

|Name|Description|Type|Key and additional info|
|----|-----------|----|----|
|Unity NAS [{#NAS.NAME}]: get|<p>Master item: returns raw JSON from Unisphere REST API.</p>|HTTP_AGENT|`unity.nas.get[{#NAS.ID}]`|
|Unity NAS [{#NAS.NAME}]: health value|<p></p>|DEPENDENT|`unity.nas.health[{#NAS.ID}]`|
|Unity NAS [{#NAS.NAME}]: home SP|<p></p>|DEPENDENT|`unity.nas.homesp[{#NAS.ID}]`|
|Unity NAS [{#NAS.NAME}]: current SP|<p></p>|DEPENDENT|`unity.nas.cursp[{#NAS.ID}]`|


### Trigger prototypes for Unity: NAS Servers discovery

|Name|Description|Expression|Severity|
|----|-----------|----------|--------|
|Unity NAS [{#NAS.NAME}]: health not OK|<p></p>|`last(/Dell EMC Unity by HTTP/unity.nas.health[{#NAS.ID}])>5 and last(/Dell EMC Unity by HTTP/unity.nas.health[{#NAS.ID}])<>7 and last(/Dell EMC Unity by HTTP/unity.nas.health[{#NAS.ID}])<25`|HIGH|
|Unity NAS [{#NAS.NAME}]: failed over to non-home SP|<p></p>|`last(/Dell EMC Unity by HTTP/unity.nas.homesp[{#NAS.ID}],#1)<>last(/Dell EMC Unity by HTTP/unity.nas.cursp[{#NAS.ID}],#1)`|WARNING|
|Unity NAS [{#NAS.NAME}]: health critical (offline/non-recoverable)|<p>NAS server offline / SMB-NFS down.</p>|`last(/Dell EMC Unity by HTTP/unity.nas.health[{#NAS.ID}])>=25`|DISASTER|


### LLD rule Unity: Replication sessions discovery

|Name|Description|Type|Key and additional info|
|----|-----------|----|----|
|Unity: Replication sessions discovery|<p></p>|HTTP_AGENT|`unity.lld.repl`|

### Item prototypes for Unity: Replication sessions discovery

|Name|Description|Type|Key and additional info|
|----|-----------|----|----|
|Unity replication [{#REPL.NAME}]: get|<p>Master item: returns raw JSON from Unisphere REST API.</p>|HTTP_AGENT|`unity.repl.get[{#REPL.ID}]`|
|Unity replication [{#REPL.NAME}]: status|<p></p>|DEPENDENT|`unity.repl.status[{#REPL.ID}]`|
|Unity replication [{#REPL.NAME}]: sync state|<p></p>|DEPENDENT|`unity.repl.sync[{#REPL.ID}]`|
|Unity replication [{#REPL.NAME}]: last sync (epoch)|<p></p>|DEPENDENT|`unity.repl.lastsync[{#REPL.ID}]`|
|Unity replication [{#REPL.NAME}]: network status|<p></p>|DEPENDENT|`unity.repl.net[{#REPL.ID}]`|
|Unity replication [{#REPL.NAME}]: lag (s)|<p></p>|CALCULATED|`unity.repl.lag[{#REPL.ID}]`|


### Trigger prototypes for Unity: Replication sessions discovery

|Name|Description|Expression|Severity|
|----|-----------|----------|--------|
|Unity replication [{#REPL.NAME}]: remote system failed to validate (0x840C / 33804)|<p>Dell Unity replication status 0x840C (Remote sy...</p>|`last(/Dell EMC Unity by HTTP/unity.repl.status[{#REPL.ID}])=33804`|HIGH|
|Unity replication [{#REPL.NAME}]: lost sync communication (0x840E / 33806)|<p>Dell Unity replication status 0x840E (Lost Sync...</p>|`last(/Dell EMC Unity by HTTP/unity.repl.status[{#REPL.ID}])=33806`|HIGH|
|Unity replication [{#REPL.NAME}]: destination pool out of space (0x840F / 33807)|<p>Dell Unity replication status 0x840F (Destinati...</p>|`last(/Dell EMC Unity by HTTP/unity.repl.status[{#REPL.ID}])=33807`|HIGH|


### LLD rule Unity: Snapshots discovery (per storage resource)

|Name|Description|Type|Key and additional info|
|----|-----------|----|----|
|Unity: Snapshots discovery (per storage resource)|<p></p>|HTTP_AGENT|`unity.lld.snap`|

### Item prototypes for Unity: Snapshots discovery (per storage resource)

|Name|Description|Type|Key and additional info|
|----|-----------|----|----|
|Unity SR [{#SR.NAME}]: snaps get|<p>Master item: returns raw JSON from Unisphere REST API.</p>|HTTP_AGENT|`unity.snap.get[{#SR.ID}]`|
|Unity SR [{#SR.NAME}]: latest snap age (s)|<p></p>|DEPENDENT|`unity.snap.lastage[{#SR.ID}]`|
|Unity SR [{#SR.NAME}]: snap count|<p></p>|DEPENDENT|`unity.snap.count[{#SR.ID}]`|


### Trigger prototypes for Unity: Snapshots discovery (per storage resource)

|Name|Description|Expression|Severity|
|----|-----------|----------|--------|
|Unity SR [{#SR.NAME}]: no recent snapshots|<p></p>|`last(/Dell EMC Unity by HTTP/unity.snap.lastage[{#SR.ID}])>{$UNITY.SNAP.AGE.WARN} and last(/Dell EMC Unity by HTTP/unity.snap.count[{#SR.ID}])>0`|WARNING|


