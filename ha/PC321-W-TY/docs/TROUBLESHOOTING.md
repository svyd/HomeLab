# Troubleshooting Guide

## Common Issues and Solutions

### 1. Sensors Show "Unavailable"

**Possible Causes:**

1. **Wrong IP Address**
   - Check the device IP hasn't changed
   - Consider assigning a static IP in your router

2. **Wrong Local Key**
   - The Local Key may have changed after a firmware update or re-pairing
   - Get a new Local Key from Tuya IoT Platform

3. **Wrong Protocol Version**
   - PC321-W-TY uses **Protocol 3.5**
   - Other versions (3.3, 3.4) will NOT work

4. **Firewall Blocking**
   - LocalTuya uses UDP ports 6666-6668
   - Make sure your firewall allows this traffic

**Solution:**
```
1. Verify device IP: ping 192.168.1.100 (your device IP)
2. Check Protocol Version is 3.5
3. Regenerate Local Key if needed
4. Check firewall rules
```

### 2. Some Sensors Work, Others Don't

**Cause:** Incorrect DPS ID or scaling factor

**Solution:**
- Verify DPS IDs match the [DPS Reference](DPS_REFERENCE.md)
- Check scaling factors are correct
- Enable debug logging to see raw values:

```yaml
logger:
  default: warning
  logs:
    custom_components.localtuya: debug
```

### 3. Values Seem Wrong (e.g., Voltage shows 1277 instead of 127.7)

**Cause:** Scaling factor not configured

**Solution:**
- Add the correct scaling factor to the entity
- Voltage: 0.1
- Current: 0.001
- Power: 0.001
- Energy: 0.01

### 4. Energy Dashboard Doesn't Track Properly

**Cause:** Wrong `state_class` configuration

**Solution:**
- Energy sensors MUST have `state_class: total_increasing`
- Power sensors should have `state_class: measurement`

### 5. Device Keeps Disconnecting

**Possible Causes:**

1. **Weak WiFi signal**
   - Move router closer or add a WiFi extender

2. **IP conflicts**
   - Assign a static IP to the device

3. **Too many polling requests**
   - Default polling interval is usually fine
   - Don't set it too aggressive (< 10 seconds)

### 6. LocalTuya Integration Won't Load

**Solution:**
1. Check HACS installed LocalTuya correctly
2. Restart Home Assistant
3. Clear browser cache
4. Check Home Assistant logs for errors:

```
Settings → System → Logs
```

### 7. Can't Find Device During Setup

**Causes:**
- Device is on a different network/VLAN
- Device IP is wrong
- Firewall blocking discovery

**Solution:**
1. Make sure device and Home Assistant are on same subnet
2. Enter the IP manually instead of using discovery
3. Check router for the device's assigned IP

### 8. Power Shows as Negative

**This is NOT a problem!**

The PC321-W-TY is bidirectional:
- **Positive values** = Importing from grid (consuming)
- **Negative values** = Exporting to grid (producing, e.g., solar)

This is the expected behavior for a bidirectional meter.

### 9. Phase C Shows Zero Values

**Cause:** Phase C is not connected

If you have a 2-phase installation (common in Americas):
- Only Phase A and Phase B will show values
- Phase C values will be 0 or near-zero
- This is normal behavior

### 10. Template Sensors Not Working

**Example Error:** `sensor.wifi_meter_activepower` is unavailable

**Solution:**
1. Check the exact entity ID in Developer Tools → States
2. Entity IDs are auto-generated based on friendly_name
3. Update template to use correct entity ID

```yaml
# Find actual entity ID
# Developer Tools → States → Search "wifi_meter"
```

## Debug Logging

Enable detailed logging to diagnose issues:

```yaml
logger:
  default: warning
  logs:
    custom_components.localtuya: debug
    custom_components.localtuya.pytuya: debug
```

After reproducing the issue, check logs at:
**Settings → System → Logs**

## Getting Help

If you still have issues:

1. **Check LocalTuya GitHub Issues**: [github.com/rospogrigio/localtuya/issues](https://github.com/rospogrigio/localtuya/issues)

2. **Home Assistant Community**: [community.home-assistant.io](https://community.home-assistant.io)

3. **Include in your post:**
   - Home Assistant version
   - LocalTuya version
   - Device model (PC321-W-TY)
   - Protocol version (3.5)
   - Relevant log entries
   - What you've already tried
