[Initial Documentation](https://github.com/atiweb/PC321-W-TY)

# PC321-W-TY WiFi Energy Meter - Home Assistant Integration

<p align="center">
  <img src="./images/pc321-w-ty-1.avif" alt="PC321-W-TY Energy Meter" width="300">
</p>

[![Home Assistant](https://img.shields.io/badge/Home%20Assistant-2024.1+-blue.svg)](https://www.home-assistant.io/)
[![HACS](https://img.shields.io/badge/HACS-Compatible-green.svg)](https://hacs.xyz/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

## 📋 Overview

This repository documents a **verified working integration** of the **PC321-W-TY** (Tuya/OWON WiFi Energy Meter) with **Home Assistant** using the **LocalTuya** integration by [xZetsubou](https://github.com/xZetsubou/hass-localtuya) (installed via custom repository in HACS).

The PC321-W-TY is a bidirectional WiFi 3-phase energy meter, ideal for monitoring electricity consumption and solar export to the grid.

> ⚠️ **Important**: This documentation is based on an actual working configuration. Many online guides have inaccuracies - this repo contains verified, tested settings that actually work.

## ⚠️ Known Issue

The official Tuya integration in Home Assistant shows this device as "unsupported" because:
- The Tuya SDK doesn't provide the information correctly
- Device data isn't exposed through the Tuya cloud API in a compatible way

**Solution**: Use **LocalTuya** for 100% local communication with the device.

## ✨ Device Features

- **3-Phase measurement** (Phase A, Phase B + Total)
- **Bidirectional**: measures grid import AND export (perfect for solar systems)
- **WiFi connectivity** using Tuya protocol (version 3.5)
- **DIN Rail mount** for electrical panel installation
- **Measures**: Voltage, Current, Active Power, Power Factor, Energy, Frequency, Temperature

## 📊 Device Specifications

| Feature | Value |
|---------|-------|
| **Model** | PC321-W-TY (Bi-Directional) |
| **Manufacturer** | OWON / Tuya |
| **Connectivity** | WiFi 2.4GHz |
| **Protocol Version** | 3.5 |
| **Phases** | 2-3 phases |
| **Max Current** | 80A per phase |

## 🔧 Integration Method: LocalTuya

This guide uses **LocalTuya** by xZetsubou (not "Tuya Local" - they are different integrations!).

> 📦 **Repository**: https://github.com/xZetsubou/hass-localtuya
>
> This is an actively maintained fork with additional features and better device support.

### Installation via HACS (Custom Repository)

1. Open **HACS** in Home Assistant
2. Go to **Integrations**
3. Click the **⋮** menu (three dots) → **Custom repositories**
4. Add the repository URL: `https://github.com/xZetsubou/hass-localtuya`
5. Select category: **Integration**
6. Click **Add**
7. Search for "LocalTuya" and install it
8. Restart Home Assistant

LocalTuya provides **100% local communication** with the device - no cloud dependency.

### Quick Links

| Document | Description |
|----------|-------------|
| [Installation](./docs/INSTALLATION.md) | Prerequisites and step-by-step installation |
| [Configuration](./docs/CONFIGURATION.md) | Complete entity configuration with DPS mappings |
| [Tuya Credentials](./docs/TUYA_CREDENTIALS.md) | How to obtain device_id and local_key |
| [DPS Reference](./docs/DPS_REFERENCE.md) | Complete Data Points table with scaling factors |
| [Troubleshooting](./docs/TROUBLESHOOTING.md) | Common issues and solutions |

## 📊 Working Configuration Summary

**Device Info (example):**
- **Device ID**: `bf12345678abcdef12xyzw`
- **Protocol Version**: `3.5`
- **Local Key**: Required (see [Tuya Credentials](./docs/TUYA_CREDENTIALS.md))

**Key Entities Configured:**

| Entity | DPS | Scaling | Unit | State Class |
|--------|-----|---------|------|-------------|
| Voltage A | 101 | 0.1 | V | measurement |
| Current A | 102 | 0.001 | A | measurement |
| Active Power A | 103 | 0.001 | kW | measurement |
| Power Factor A | 104 | - | % | measurement |
| Energy Consumed A | 106 | 0.01 | kWh | total_increasing |
| Reverse Energy A | 107 | 0.01 | kWh | total_increasing |
| Voltage B | 111 | 0.1 | V | measurement |
| Current B | 112 | 0.001 | A | measurement |
| Active Power B | 113 | 0.001 | kW | measurement |
| Power Factor B | 114 | - | % | measurement |
| Energy Consumed B | 116 | 0.01 | kWh | total_increasing |
| Reverse Energy B | 117 | 0.01 | kWh | total_increasing |
| Total Energy Consumed | 131 | 0.01 | kWh | total_increasing |
| Total Current | 132 | 0.001 | A | measurement |
| Total Active Power | 133 | 0.001 | kW | measurement |
| Frequency | 135 | - | Hz | measurement |
| Temperature | 136 | 0.1 | °C | measurement |
| Total Reverse Energy | 139 | 0.01 | kWh | total_increasing |

## 🏠 Template Sensors for Energy Dashboard

Example template sensors for solar system integration (from working configuration):

```yaml
template:
  - sensor:
      # Grid power with sign (W)
      # + = importing from grid | - = exporting to grid
      - name: "grid_power_signed"
        unique_id: grid_power_signed
        unit_of_measurement: W
        device_class: power
        state_class: measurement
        state: >-
          {{ (states('sensor.wifi_meter_activepower')|float(0) * 1000) | round(0) }}

      # Grid Import (W, positive only)
      - name: "grid_import_power"
        unique_id: grid_import_power
        unit_of_measurement: W
        device_class: power
        state_class: measurement
        state: >-
          {% set p = states('sensor.grid_power_signed')|float(0) %}
          {{ max(p, 0) | round(0) }}

      # Grid Export (W, positive only)
      - name: "grid_export_power"
        unique_id: grid_export_power
        unit_of_measurement: W
        device_class: power
        state_class: measurement
        state: >-
          {% set p = states('sensor.grid_power_signed')|float(0) %}
          {{ max(-p, 0) | round(0) }}

      # House consumption (W): House = Solar + Import - Export
      - name: "house_consumption_power"
        unique_id: house_consumption_power
        unit_of_measurement: W
        device_class: power
        state_class: measurement
        state: >-
          {% set s = states('sensor.solar_ac_power_total')|float(0) %}
          {% set gi = states('sensor.grid_import_power')|float(0) %}
          {% set ge = states('sensor.grid_export_power')|float(0) %}
          {{ max(s + gi - ge, 0) | round(0) }}
```

## 📈 Energy Dashboard Integration

The PC321-W-TY integrates with Home Assistant's **Energy Dashboard**:

- **Grid Consumption**: Use `TotalEnergyConsumed` (DPS 131) with `state_class: total_increasing`
- **Return to Grid**: Use `reverse_energy_t` (DPS 139) with `state_class: total_increasing`

➡️ [Energy Dashboard Configuration](examples/energy-dashboard/README.md)

## 📁 Repository Structure

```
PC321-W-TY/
├── README.md                    # This file
├── LICENSE                      # MIT License
├── docs/
│   ├── INSTALLATION.md          # Installation guide
│   ├── CONFIGURATION.md         # LocalTuya configuration
│   ├── TUYA_CREDENTIALS.md      # How to get device_id and local_key
│   ├── DPS_REFERENCE.md         # DPS (Data Points) reference
│   └── TROUBLESHOOTING.md       # Common issues and solutions
├── examples/
│   ├── localtuya/               # LocalTuya configuration examples
│   └── energy-dashboard/        # Energy dashboard setup
└── images/                      # Device images
```

## 📄 License

This project is licensed under the MIT License - see [LICENSE](LICENSE) for details.

## 🙏 Acknowledgments

- Home Assistant Community
- [LocalTuya](https://github.com/xZetsubou/hass-localtuya) by xZetsubou (actively maintained fork)
- Tuya IoT Platform

---

<p align="center">
  <i>If you found this useful, please give it a ⭐</i>
</p>
