# LocalTuya Configuration Examples

This folder contains configuration examples for integrating the PC321-W-TY with LocalTuya.

## configuration.yaml Templates

### Template Sensors for Solar System

Add these to your `configuration.yaml` to create useful derived sensors:

```yaml
template:
  - sensor:
      # Grid power with sign (Watts)
      # Positive = importing from grid
      # Negative = exporting to grid
      - name: "grid_power_signed"
        unique_id: grid_power_signed
        unit_of_measurement: W
        device_class: power
        state_class: measurement
        state: >-
          {{ (states('sensor.wifi_meter_activepower')|float(0) * 1000) | round(0) }}

      # Grid Import Power (W) - only positive values
      - name: "grid_import_power"
        unique_id: grid_import_power
        unit_of_measurement: W
        device_class: power
        state_class: measurement
        state: >-
          {% set p = states('sensor.grid_power_signed')|float(0) %}
          {{ max(p, 0) | round(0) }}

      # Grid Export Power (W) - only positive values
      - name: "grid_export_power"
        unique_id: grid_export_power
        unit_of_measurement: W
        device_class: power
        state_class: measurement
        state: >-
          {% set p = states('sensor.grid_power_signed')|float(0) %}
          {{ max(-p, 0) | round(0) }}

      # House Consumption (W)
      # Formula: House = Solar + Grid Import - Grid Export
      - name: "house_consumption_power"
        unique_id: house_consumption_power
        unit_of_measurement: W
        device_class: power
        state_class: measurement
        state: >-
          {% set solar = states('sensor.solar_power_total')|float(0) %}
          {% set grid_import = states('sensor.grid_import_power')|float(0) %}
          {% set grid_export = states('sensor.grid_export_power')|float(0) %}
          {{ max(solar + grid_import - grid_export, 0) | round(0) }}
```

### Notes on Entity Names

The entity ID `sensor.wifi_meter_activepower` comes from LocalTuya and is based on:
- The device name you configured ("WIFi_Meter")
- The friendly name of the entity ("ActivePower")

If you used different names, adjust accordingly.

## Automation Examples

### High Power Consumption Alert

```yaml
automation:
  - alias: "High Power Consumption Alert"
    description: "Notify when power consumption exceeds 5kW"
    trigger:
      - platform: numeric_state
        entity_id: sensor.wifi_meter_activepower
        above: 5
    action:
      - service: notify.mobile_app_your_phone
        data:
          title: "⚡ High Power Alert"
          message: "Current consumption: {{ states('sensor.wifi_meter_activepower') }} kW"
```

### Grid Export Notification (Solar Excess)

```yaml
automation:
  - alias: "Solar Export Notification"
    description: "Notify when exporting solar power to grid"
    trigger:
      - platform: numeric_state
        entity_id: sensor.grid_export_power
        above: 1000
        for:
          minutes: 5
    action:
      - service: notify.mobile_app_your_phone
        data:
          title: "☀️ Solar Surplus"
          message: "Exporting {{ states('sensor.grid_export_power') }}W to grid"
```

### Track Daily Energy Usage

```yaml
utility_meter:
  daily_energy_consumed:
    source: sensor.wifi_meter_totalenergyconsumed
    cycle: daily
  
  daily_energy_exported:
    source: sensor.wifi_meter_reverse_energy_t
    cycle: daily
    
  monthly_energy_consumed:
    source: sensor.wifi_meter_totalenergyconsumed
    cycle: monthly
    
  monthly_energy_exported:
    source: sensor.wifi_meter_reverse_energy_t
    cycle: monthly
```

## Dashboard Card Examples

### Power Flow Card (requires custom card)

```yaml
type: custom:power-flow-card
entities:
  grid: sensor.grid_power_signed
  solar: sensor.solar_power_total
  home: sensor.house_consumption_power
```

### Simple Entities Card

```yaml
type: entities
title: Energy Meter
entities:
  - entity: sensor.wifi_meter_voltagea
    name: Voltage A
  - entity: sensor.wifi_meter_voltageb
    name: Voltage B
  - entity: sensor.wifi_meter_activepower
    name: Total Power
  - entity: sensor.wifi_meter_totalenergyconsumed
    name: Total Consumed
  - entity: sensor.wifi_meter_reverse_energy_t
    name: Total Exported
  - entity: sensor.wifi_meter_frequency
    name: Frequency
  - entity: sensor.wifi_meter_temperature
    name: Temperature
```
