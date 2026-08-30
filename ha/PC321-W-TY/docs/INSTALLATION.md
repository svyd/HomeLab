# Installation Guide

## Prerequisites

Before starting, make sure you have:

1. **Home Assistant** installed and running (version 2024.1 or higher recommended)
2. **HACS** (Home Assistant Community Store) installed
3. **Tuya IoT Platform** account (for obtaining credentials)
4. **PC321-W-TY** already configured and working in Smart Life/Tuya Smart app
5. **Same network**: The energy meter and Home Assistant must be on the same local network

## Step 1: Install LocalTuya via HACS

1. Open Home Assistant
2. Go to **HACS** → **Integrations**
3. Click the **+ Explore & Download Repositories** button
4. Search for **LocalTuya**
5. Click **Download**
6. **Restart Home Assistant**

## Step 2: Obtain Tuya Credentials

You need to obtain:
- **Device ID** 
- **Local Key**

See [TUYA_CREDENTIALS.md](TUYA_CREDENTIALS.md) for detailed instructions.

## Step 3: Find Device IP Address

The PC321-W-TY needs a static IP or you need to know its current IP address.

**Methods to find the IP:**
1. Check your router's DHCP client list
2. Use a network scanner app (like Fing)
3. Check the Tuya IoT Platform device list

**Recommended**: Assign a static IP to the device in your router.

## Step 4: Add LocalTuya Integration

1. Go to **Settings** → **Devices & Services**
2. Click **+ Add Integration**
3. Search for **LocalTuya**
4. Select **Add a new device manually**
5. Enter:
   - **Name**: `WIFi_Meter` (or your preferred name)
   - **Host**: Device IP address (e.g., `192.168.0.202`)
   - **Device ID**: From Tuya IoT Platform
   - **Local Key**: From Tuya IoT Platform
   - **Protocol Version**: `3.5`

## Step 5: Configure Entities

After adding the device, you need to configure each entity (sensor).

See [CONFIGURATION.md](CONFIGURATION.md) for the complete entity configuration.

## Verification

After configuration, verify that:

1. All sensors show numeric values (not "unknown" or "unavailable")
2. Voltage readings are approximately correct (e.g., ~120V or ~220V depending on your region)
3. Power readings change when you turn appliances on/off
4. Energy counters increment over time

## Troubleshooting

If sensors show "unavailable":
- Check the device IP address is correct
- Verify the Local Key hasn't changed
- Make sure Protocol Version is `3.5`
- Check firewall isn't blocking UDP ports 6666-6668

See [TROUBLESHOOTING.md](TROUBLESHOOTING.md) for more help.
