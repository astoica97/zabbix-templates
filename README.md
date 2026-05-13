# Zabbix Templates

This repository contains Zabbix monitoring templates for various infrastructure components.

## Structure

```
templates/
├── storage/
│   └── dell_unity_xt/
│       └── 7.0/
│           ├── template_dell_unity_xt.yaml
│           └── README.md
```

## Usage

Each template folder contains:
- `template_[name].yaml` - The Zabbix template file (importable via Zabbix UI)
- `README.md` - Documentation, setup instructions, and configuration guide

### Importing a Template

1. In Zabbix frontend, go to **Monitoring** → **Templates**
2. Click **Import**
3. Select the `.yaml` file
4. Confirm and apply

### Configuring a Template

After importing a template:
1. Link it to your desired host
2. Update the host/template macros with your environment-specific values
3. Adjust trigger thresholds as needed
4. Wait for data collection to begin

## Templates Available

### Storage

- **Dell EMC Unity by HTTP** (v7.0)
  - Monitoring Dell EMC Unity storage arrays via REST API
  - [Details](templates/storage/dell_unity_xt/7.0/README.md)

## Requirements

- Zabbix Server 7.0+
- Network connectivity from Zabbix to monitored resources
- Appropriate user credentials/API keys for target systems

## License

These templates are provided as-is for monitoring purposes.

---

**Repository Last Updated:** 2026-05-13
