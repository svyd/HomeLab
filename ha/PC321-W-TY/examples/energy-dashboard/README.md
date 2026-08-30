# Energy Dashboard Configuration

## Overview

Home Assistant's Energy Dashboard can track:
- **Grid Consumption** - Energy imported from the grid
- **Return to Grid** - Energy exported to the grid (solar surplus)
- **Solar Production** - Total solar energy generated

## Required Entities

From the PC321-W-TY, you need:

| Purpose | Entity | DPS |
|---------|--------|-----|
| Grid Consumption | `sensor.wifi_meter_totalenergyconsumed` | 131 |
| Return to Grid | `sensor.wifi_meter_reverse_energy_t` | 139 |

## Important Configuration

These sensors MUST have:
- `device_class: energy`
- `state_class: total_increasing`
- `unit_of_measurement: kWh`

This is already configured if you followed the [Configuration Guide](../../docs/CONFIGURATION.md).

## Setting Up Energy Dashboard

1. Go to **Settings** → **Dashboards** → **Energy**
2. Or navigate directly to `/config/energy`

### Electricity Grid Section

1. Click **Add Consumption**
2. Select `sensor.wifi_meter_totalenergyconsumed`
3. Click **Add Return to Grid** (if you have solar)
4. Select `sensor.wifi_meter_reverse_energy_t`

### Solar Panels Section (Optional)

If you have a solar inverter integrated:

1. Click **Add Solar Production**
2. Select your solar production sensor

## Cost Tracking (Optional)

You can add electricity costs:

1. In the Energy Dashboard configuration
2. Click the pencil icon next to Grid Consumption
3. Add a static price or use a price entity

Example static configuration:
- Price: 0.15 (per kWh)
- Currency: USD (or your currency)

## Sample Dashboard Configuration

```yaml
# Example energy dashboard entities
energy:
  electricity:
    grid_consumption:
      - sensor.wifi_meter_totalenergyconsumed
    grid_export:
      - sensor.wifi_meter_reverse_energy_t
    solar_production:
      - sensor.solar_panel_energy_total  # Your solar sensor
```

## Troubleshooting

### Data Not Showing

1. **Wait**: Energy Dashboard needs time to collect data (1-2 hours minimum)
2. **Check Statistics**: Go to Developer Tools → Statistics and verify your sensors

### Reset Statistics

If you see incorrect data:

1. Go to **Developer Tools** → **Statistics**
2. Find the problematic sensor
3. Click **Fix Issues** if available

### Entity Not Selectable

Your sensor needs:
- `device_class: energy`
- `state_class: total_increasing`
- `unit_of_measurement: kWh`

Check your LocalTuya entity configuration.

## Per-Phase Tracking

If you want to track energy per phase:

```yaml
# Phase A
sensor.wifi_meter_energyconsumeda  # DPS 106
sensor.wifi_meter_reverse_energy_a  # DPS 107

# Phase B  
sensor.wifi_meter_energyconsumedb  # DPS 116
sensor.wifi_meter_reverse_energy_b  # DPS 117
```

These can be added as additional consumption/return sensors in the Energy Dashboard for detailed per-phase tracking.
