# LocalTuya Entity Configuration

This document contains the **exact working configuration** for the PC321-W-TY energy meter entities in LocalTuya.

## Device Setup

When adding the device in LocalTuya, use these settings:

| Setting | Value |
|---------|-------|
| **Name** | WIFi_Meter |
| **Host** | Your device IP (e.g., 192.168.1.100) |
| **Device ID** | Your device ID |
| **Local Key** | Your local key |
| **Protocol Version** | 3.5 |
| **Enable Debug** | false |

## Entity Configuration

Add each entity as a **Sensor** platform with the following settings:

### Phase A Sensors

#### Voltage A
| Setting | Value |
|---------|-------|
| **Platform** | sensor |
| **Friendly Name** | VoltageA |
| **DPS ID** | 101 |
| **Device Class** | voltage |
| **Unit of Measurement** | V |
| **Scaling** | 0.1 |
| **State Class** | measurement |

#### Current A
| Setting | Value |
|---------|-------|
| **Platform** | sensor |
| **Friendly Name** | CurrentA |
| **DPS ID** | 102 |
| **Device Class** | current |
| **Unit of Measurement** | A |
| **Scaling** | 0.001 |
| **State Class** | measurement |

#### Active Power A
| Setting | Value |
|---------|-------|
| **Platform** | sensor |
| **Friendly Name** | ActivePowerA |
| **DPS ID** | 103 |
| **Device Class** | power |
| **Unit of Measurement** | kW |
| **Scaling** | 0.001 |
| **State Class** | measurement |

#### Power Factor A
| Setting | Value |
|---------|-------|
| **Platform** | sensor |
| **Friendly Name** | PowerFactorA |
| **DPS ID** | 104 |
| **Device Class** | power_factor |
| **Unit of Measurement** | % |
| **Scaling** | (none) |
| **State Class** | measurement |

#### Energy Consumed A
| Setting | Value |
|---------|-------|
| **Platform** | sensor |
| **Friendly Name** | EnergyConsumedA |
| **DPS ID** | 106 |
| **Device Class** | energy |
| **Unit of Measurement** | kWh |
| **Scaling** | 0.01 |
| **State Class** | total_increasing |

#### Reverse Energy A (Export)
| Setting | Value |
|---------|-------|
| **Platform** | sensor |
| **Friendly Name** | reverse_energy_a |
| **DPS ID** | 107 |
| **Device Class** | energy |
| **Unit of Measurement** | kWh |
| **Scaling** | 0.01 |
| **State Class** | total_increasing |

---

### Phase B Sensors

#### Voltage B
| Setting | Value |
|---------|-------|
| **Platform** | sensor |
| **Friendly Name** | VoltageB |
| **DPS ID** | 111 |
| **Device Class** | voltage |
| **Unit of Measurement** | V |
| **Scaling** | 0.1 |
| **State Class** | measurement |

#### Current B
| Setting | Value |
|---------|-------|
| **Platform** | sensor |
| **Friendly Name** | CurrentB |
| **DPS ID** | 112 |
| **Device Class** | current |
| **Unit of Measurement** | A |
| **Scaling** | 0.001 |
| **State Class** | measurement |

#### Active Power B
| Setting | Value |
|---------|-------|
| **Platform** | sensor |
| **Friendly Name** | ActivePowerB |
| **DPS ID** | 113 |
| **Device Class** | power |
| **Unit of Measurement** | kW |
| **Scaling** | 0.001 |
| **State Class** | measurement |

#### Power Factor B
| Setting | Value |
|---------|-------|
| **Platform** | sensor |
| **Friendly Name** | PowerFactorB |
| **DPS ID** | 114 |
| **Device Class** | power_factor |
| **Unit of Measurement** | % |
| **Scaling** | (none) |
| **State Class** | measurement |

#### Energy Consumed B
| Setting | Value |
|---------|-------|
| **Platform** | sensor |
| **Friendly Name** | EnergyConsumedB |
| **DPS ID** | 116 |
| **Device Class** | energy |
| **Unit of Measurement** | kWh |
| **Scaling** | 0.01 |
| **State Class** | total_increasing |

#### Reverse Energy B (Export)
| Setting | Value |
|---------|-------|
| **Platform** | sensor |
| **Friendly Name** | reverse_energy_b |
| **DPS ID** | 117 |
| **Device Class** | energy |
| **Unit of Measurement** | kWh |
| **Scaling** | 0.01 |
| **State Class** | total_increasing |

---

### Total / Combined Sensors

#### Total Energy Consumed
| Setting | Value |
|---------|-------|
| **Platform** | sensor |
| **Friendly Name** | TotalEnergyConsumed |
| **DPS ID** | 131 |
| **Device Class** | energy |
| **Unit of Measurement** | kWh |
| **Scaling** | 0.01 |
| **State Class** | total_increasing |

#### Total Current
| Setting | Value |
|---------|-------|
| **Platform** | sensor |
| **Friendly Name** | Current |
| **DPS ID** | 132 |
| **Device Class** | current |
| **Unit of Measurement** | A |
| **Scaling** | 0.001 |
| **State Class** | measurement |

#### Total Active Power
| Setting | Value |
|---------|-------|
| **Platform** | sensor |
| **Friendly Name** | ActivePower |
| **DPS ID** | 133 |
| **Device Class** | power |
| **Unit of Measurement** | kW |
| **Scaling** | 0.001 |
| **State Class** | measurement |

#### Frequency
| Setting | Value |
|---------|-------|
| **Platform** | sensor |
| **Friendly Name** | Frequency |
| **DPS ID** | 135 |
| **Device Class** | frequency |
| **Unit of Measurement** | Hz |
| **Scaling** | (none) |
| **State Class** | measurement |

#### Temperature
| Setting | Value |
|---------|-------|
| **Platform** | sensor |
| **Friendly Name** | Temperature |
| **DPS ID** | 136 |
| **Device Class** | temperature |
| **Unit of Measurement** | °C |
| **Scaling** | 0.1 |
| **State Class** | measurement |

#### Total Reverse Energy (Export)
| Setting | Value |
|---------|-------|
| **Platform** | sensor |
| **Friendly Name** | reverse_energy_t |
| **DPS ID** | 139 |
| **Device Class** | energy |
| **Unit of Measurement** | kWh |
| **Scaling** | 0.01 |
| **State Class** | total_increasing |

---

## Important Notes

### Scaling Factors

The raw DPS values from the device need to be scaled:

- **Voltage**: Raw value ÷ 10 = Volts (scaling: 0.1)
- **Current**: Raw value ÷ 1000 = Amps (scaling: 0.001)  
- **Power**: Raw value ÷ 1000 = kW (scaling: 0.001)
- **Energy**: Raw value ÷ 100 = kWh (scaling: 0.01)
- **Temperature**: Raw value ÷ 10 = °C (scaling: 0.1)

### State Classes

- Use `measurement` for instantaneous values (voltage, current, power, frequency, temperature)
- Use `total_increasing` for cumulative energy counters (consumed and reverse energy)

### Bidirectional Power

The PC321-W-TY supports bidirectional measurement:
- **Positive power values** = Consuming from grid (import)
- **Negative power values** = Exporting to grid (solar excess)

The `reverse_energy` sensors (DPS 107, 117, 139) track total energy exported.

## Resulting Entity IDs

After configuration, your entities will be named like:
- `sensor.wifi_meter_voltagea`
- `sensor.wifi_meter_currenta`
- `sensor.wifi_meter_activepowera`
- `sensor.wifi_meter_activepower` (total)
- `sensor.wifi_meter_totalenergyconsumed`
- `sensor.wifi_meter_reverse_energy_t`
- etc.
