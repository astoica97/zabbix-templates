# Dell EMC Unity by HTTP

Zabbix template for monitoring Dell EMC Unity storage arrays (XT 380/480/680/880, All-Flash, Hybrid) via Unisphere Management REST API.

## Supported versions

- Zabbix 7.0
- Dell EMC Unity OE 5.x

## Features

This template provides comprehensive monitoring for Dell EMC Unity storage systems through HTTP-based REST API calls.

### System Monitoring

- System health status and health descriptions
- Software version (short and full version information)
- System identification (name, model, serial number, platform)

### Hardware Monitoring (Low-Level Discovery)

- **Storage Processors (SP):** CPU usage, performance metrics
- **Disks:** Health status, activity monitoring
- **DAEs (Disk Array Enclosures):** Temperature, PSU, fans
- **Power Supplies (PSU):** Status and health
- **Batteries:** Charge status, health
- **IO Modules:** Health and status
- **LCC/SSC:** Link control and system controllers
- **SSD:** Status and performance
- **Memory:** Utilization metrics

### Storage Resources (Low-Level Discovery)

- **Pools:** Usage percentage, subscription percentage, deduplication/compression ratios
- **LUNs:** Capacity, allocated size, thin provisioning metrics
- **Filesystems:** Capacity, usage percentage, provisioning
- **NAS Servers:** Failover status, availability

### Replication & Snapshots

- **Replication Sessions:** Status, synchronization lag, replication state
- **Snapshots:** Age monitoring, creation tracking per storage resource

### Performance Metrics

- Real-time performance queries via metricRealTimeQuery API
- CPU usage (Storage Processors)
- IOPS (Input/Output Operations Per Second)
- Latency metrics
- Throughput measurements

### Alerting

- Active alerts aggregation by severity level:
  - Emergency (0)
  - Alert (1)
  - Critical (2)
  - Error (3)
  - Warning (4)
  - Notice (5)
  - Info (6)
  - Debug (7)

## Prerequisites

- Dell EMC Unity Unisphere Management interface must be accessible via HTTPS
- Read-only user credentials for the REST API
- Network connectivity from Zabbix agent/server to the Unisphere management IP/FQDN

## Configuration

### Macros

The following user macros must be configured:

| Macro | Default | Description |
|-------|---------|-------------|
| `{$UNITY.URL}` | `https://unity-mgmt.example.com` | Base URL of Unisphere REST API (without trailing slash) |
| `{$UNITY.USER}` | `monitor` | Read-only user for REST API |
| `{$UNITY.PASSWORD}` | `` | Password for the user (stored as secret text) |
| `{$UNITY.HTTP.TIMEOUT}` | `30s` | HTTP request timeout |
| `{$UNITY.POOL.PUSED.WARN}` | `90` | Pool usage warning threshold (%) |
| `{$UNITY.POOL.PUSED.HIGH}` | `95` | Pool usage high threshold (%) |
| `{$UNITY.POOL.PUSED.CRIT}` | `100` | Pool usage critical threshold (%) |
| `{$UNITY.POOL.SUBSCRIBED.WARN}` | `150` | Pool subscription warning threshold (%) |
| `{$UNITY.FS.PUSED.WARN}` | `90` | Filesystem usage warning threshold (%) |
| `{$UNITY.FS.PUSED.HIGH}` | `95` | Filesystem usage high threshold (%) |
| `{$UNITY.FS.PUSED.CRIT}` | `100` | Filesystem usage critical threshold (%) |
| `{$UNITY.LUN.PUSED.WARN}` | `90` | LUN thin provisioning warning threshold (%) |
| `{$UNITY.SP.CPU.WARN}` | `80` | Storage Processor CPU warning threshold (%) |
| `{$UNITY.SP.CPU.HIGH}` | `90` | Storage Processor CPU high threshold (%) |
| `{$UNITY.LUN.LLD.MATCHES}` | `.*` | Regex filter for LUN names to INCLUDE |
| `{$UNITY.LUN.LLD.NOT_MATCHES}` | `CHANGE_IF_NEEDED` | Regex filter for LUN names to EXCLUDE |
| `{$UNITY.FS.LLD.MATCHES}` | `.*` | Regex filter for Filesystem names to INCLUDE |
| `{$UNITY.FS.LLD.NOT_MATCHES}` | `CHANGE_IF_NEEDED` | Regex filter for Filesystem names to EXCLUDE |
| `{$UNITY.POOL.LLD.MATCHES}` | `.*` | Regex filter for Pool names to INCLUDE |
| `{$UNITY.POOL.LLD.NOT_MATCHES}` | `CHANGE_IF_NEEDED` | Regex filter for Pool names to EXCLUDE |
| `{$UNITY.SNAP.AGE.WARN}` | `604800` | Maximum snapshot age in seconds (default: 7 days) |
| `{$UNITY.PERF.INTERVAL}` | `60` | Real-time metric query window in seconds |

### Setup Steps

1. Link the template to your Zabbix host
2. Ensure the host has network connectivity to the Unisphere management interface
3. Edit the host macros (or template macros) to set:
   - `{$UNITY.URL}`: The HTTPS URL of your Unisphere management interface
   - `{$UNITY.USER}`: A read-only service account username
   - `{$UNITY.PASSWORD}`: The password for that account
4. Adjust threshold macros as needed for your environment
5. Modify the regex filters for LLD discovery if you want to exclude specific resources

## Authentication

The template uses Basic Authentication with an additional custom header:

- **Auth Type:** HTTP Basic Authentication
- **Custom Header:** `X-EMC-REST-CLIENT: true`
- **Content-Type:** `application/json`
- **SSL/TLS:** Certificate validation is disabled (suitable for self-signed certificates)

## Value Mappings

The template includes the following value mappings for human-readable status displays:

- **Unity health value:** Maps numeric health codes (0, 5, 7, 10, 15, 20, 25, 30) to health states
- **Unity boolean:** Maps 0/1 to false/true
- **Unity replication status:** Maps numeric codes to replication states (Idle, OK, Auto sync, Manual sync, Updating, Failed over, etc.)
- **Unity alert severity:** Maps numeric codes to severity levels (Emergency, Alert, Critical, Error, Warning, Notice, Info, Debug)

## Data Collection

### Update Intervals

- **System information:** Every 5 minutes
- **Software version:** Every 1 hour
- **Alerts:** Every 2 minutes
- **LLD discovery:** Default Zabbix interval (usually 1 hour)
- **Performance metrics:** Every 60 seconds

### Data Flow

1. HTTP agent items fetch raw JSON from Unisphere REST API
2. Dependent items parse JSON using JSONPATH preprocessing
3. JavaScript preprocessing handles complex value transformations
4. Low-level discovery creates individual items for pools, LUNs, filesystems, etc.
5. Triggers evaluate metrics against configured thresholds

## Supported REST API Endpoints

- `/api/instances/system/0` - System information
- `/api/types/installedSoftwareVersion/instances` - Software versions
- `/api/types/alert/instances` - Active alerts
- `/api/types/pool/instances` - Storage pools
- `/api/types/lun/instances` - LUNs
- `/api/types/filesystem/instances` - Filesystems
- `/api/types/nasServer/instances` - NAS servers
- `/api/types/replicationSession/instances` - Replication sessions
- `/api/types/snap/instances` - Snapshots
- `/api/types/hardware/{type}/instances` - Hardware inventory (SP, disks, DAEs, fans, PSU, etc.)
- `/api/metricRealTimeQuery` - Real-time performance metrics

## Troubleshooting

### Connection Issues

- Verify the HTTPS URL is correct and accessible from the Zabbix server
- Check that the user account has read-only API permissions
- Ensure the `X-EMC-REST-CLIENT: true` header is being sent
- Verify certificate validation isn't blocking the connection (template disables it for self-signed certs)

### No Data Being Collected

- Check that the REST API is responding to the configured user credentials
- Verify macros are properly set at the host level
- Check Zabbix agent/server logs for HTTP errors
- Ensure the Unisphere API is accessible and the user has sufficient permissions

### Threshold Tuning

- Adjust the pool, filesystem, and LUN thresholds based on your capacity planning
- Monitor trends over time to set appropriate warning levels
- Consider your organization's SLA and risk tolerance when setting critical thresholds

## Compatibility

- **Zabbix Versions:** 7.0
- **Unity OE Versions:** 5.x and later
- **Unity Models:** XT 380, XT 480, XT 680, XT 880, All-Flash, Hybrid

## Template Metadata

- **Vendor:** ECS
- **Version:** 7.0-1
- **Group:** Templates CSN
- **UUID:** 16b0b3ea67a542979585801d74cdae95

## License

This template is provided as-is for monitoring purposes. Ensure compliance with Dell EMC Unity licensing terms when using monitoring solutions.

## Support

For issues or questions about this Zabbix template, refer to:
- Dell EMC Unisphere Management documentation
- Zabbix documentation for REST API integration
- Your Zabbix system administrator

---

**Template Last Updated:** 2026-05-13
**Zabbix Version:** 7.0
