# DPS Reference - PC321-W-TY

## What is DPS?

**DPS (Data Point Service)** is Tuya's system for identifying different data points/features of a device. Each DPS ID corresponds to a specific measurement or control function.

## Complete DPS Map

This table shows all known DPS values for the PC321-W-TY energy meter based on actual device communication:

### Phase A (1xx series)

| DPS ID | Name | Raw Value Example | Scaling | Unit | Description |
|--------|------|-------------------|---------|------|-------------|
| 101 | VoltageA | 1277 | ÷10 (0.1) | V | Phase A voltage (127.7V) |
| 102 | CurrentA | 3397 | ÷1000 (0.001) | A | Phase A current (3.397A) |
| 103 | ActivePowerA | 314 | ÷1000 (0.001) | kW | Phase A active power (0.314kW) |
| 104 | PowerFactorA | 70 | - | % | Phase A power factor (70%) |
| 106 | EnergyConsumedA | 8679 | ÷100 (0.01) | kWh | Phase A total energy imported (86.79kWh) |
| 107 | ReverseEnergyA | 324 | ÷100 (0.01) | kWh | Phase A total energy exported (3.24kWh) |

### Phase B (11x series)

| DPS ID | Name | Raw Value Example | Scaling | Unit | Description |
|--------|------|-------------------|---------|------|-------------|
| 111 | VoltageB | 1274 | ÷10 (0.1) | V | Phase B voltage (127.4V) |
| 112 | CurrentB | 3922 | ÷1000 (0.001) | A | Phase B current (3.922A) |
| 113 | ActivePowerB | -264 | ÷1000 (0.001) | kW | Phase B active power (-0.264kW, negative = export) |
| 114 | PowerFactorB | 54 | - | % | Phase B power factor (54%) |
| 116 | EnergyConsumedB | 6372 | ÷100 (0.01) | kWh | Phase B total energy imported (63.72kWh) |
| 117 | ReverseEnergyB | 1007 | ÷100 (0.01) | kWh | Phase B total energy exported (10.07kWh) |

### Phase C (12x series) - If connected

| DPS ID | Name | Raw Value Example | Scaling | Unit | Description |
|--------|------|-------------------|---------|------|-------------|
| 121 | VoltageC | 3 | ÷10 (0.1) | V | Phase C voltage |
| 122 | CurrentC | 0 | ÷1000 (0.001) | A | Phase C current |
| 123 | ActivePowerC | 0 | ÷1000 (0.001) | kW | Phase C active power |
| 124 | PowerFactorC | 0 | - | % | Phase C power factor |
| 126 | EnergyConsumedC | 0 | ÷100 (0.01) | kWh | Phase C total energy imported |
| 127 | ReverseEnergyC | 0 | ÷100 (0.01) | kWh | Phase C total energy exported |

### Totals (13x series)

| DPS ID | Name | Raw Value Example | Scaling | Unit | Description |
|--------|------|-------------------|---------|------|-------------|
| 131 | TotalEnergyConsumed | 15051 | ÷100 (0.01) | kWh | Total energy imported (150.51kWh) |
| 132 | TotalCurrent | 7319 | ÷1000 (0.001) | A | Total current (7.319A) |
| 133 | TotalActivePower | 50 | ÷1000 (0.001) | kW | Total active power (0.050kW) |
| 135 | Frequency | 60 | - | Hz | Grid frequency (60Hz) |
| 136 | Temperature | 376 | ÷10 (0.1) | °C | Device temperature (37.6°C) |
| 137 | Unknown137 | 68 | ? | ? | Unknown purpose |
| 139 | TotalReverseEnergy | 1331 | ÷100 (0.01) | kWh | Total energy exported (13.31kWh) |

## Raw DPS String Example

This is an actual DPS string captured from a working device:

```
101 ( value: 1277 )
102 ( value: 3397 )
103 ( value: 314 )
104 ( value: 70 )
106 ( value: 8679 )
107 ( value: 324 )
111 ( value: 1274 )
112 ( value: 3922 )
113 ( value: -264 )
114 ( value: 54 )
116 ( value: 6372 )
117 ( value: 1007 )
121 ( value: 3 )
122 ( value: 0 )
123 ( value: 0 )
124 ( value: 0 )
126 ( value: 0 )
127 ( value: 0 )
131 ( value: 15051 )
132 ( value: 7319 )
133 ( value: 50 )
135 ( value: 60 )
136 ( value: 376 )
137 ( value: 68 )
139 ( value: 1331 )
```

## Notes

### Negative Power Values

When exporting power to the grid (e.g., from solar panels), power values will be **negative**:
- `103 ( value: -264 )` means Phase A is exporting 0.264 kW

### Unused Phases

If a phase isn't connected, its values will show as 0 or very small numbers (like 3 for voltage due to coupling).

### Energy Counters

DPS 106, 107, 116, 117, 126, 127, 131, 139 are **cumulative counters** that keep increasing. Use `state_class: total_increasing` in Home Assistant for proper statistics.

### Protocol Version

This device requires **Protocol Version 3.5** for communication.
