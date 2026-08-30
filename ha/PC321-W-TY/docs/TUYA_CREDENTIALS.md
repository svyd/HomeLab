# How to Obtain Tuya Credentials

To use LocalTuya, you need to obtain the **Device ID** and **Local Key** for your PC321-W-TY.

## What You Need

| Credential | Description |
|------------|-------------|
| **Device ID** | Unique identifier for your device (e.g., `bf12345678abcdef12xyzw`) |
| **Local Key** | 16-character encryption key for local communication |

## Method 1: Tuya IoT Platform (Recommended)

### Step 1: Create a Tuya Developer Account

1. Go to [Tuya IoT Platform](https://iot.tuya.com/)
2. Click **Register** and create an account
3. Verify your email

### Step 2: Create a Cloud Project

1. Log in to Tuya IoT Platform
2. Go to **Cloud** → **Development** → **Create Cloud Project**
3. Fill in:
   - **Project Name**: Home Assistant (or any name)
   - **Industry**: Smart Home
   - **Development Method**: Custom
   - **Data Center**: Choose your region (Western America, Central Europe, etc.)
4. Click **Create**

### Step 3: Link Your Smart Life Account

1. In your project, go to **Devices** → **Link Tuya App Account**
2. Click **Add App Account**
3. Open Smart Life/Tuya Smart app on your phone
4. Go to **Profile** → **Scan QR Code**
5. Scan the QR code shown on the Tuya IoT Platform
6. Confirm the linking in the app

### Step 4: Get Device Information

1. In **Devices** → **All Devices**, find your PC321-W-TY
2. Click on the device
3. Copy the **Device ID**
4. Note: The Local Key is NOT directly visible here

### Step 5: Get the Local Key

The Local Key requires API access:

1. Go to **Cloud** → **API Explorer**
2. Select **Device Management** → **Get Device Information**
3. Enter your **Device ID**
4. Click **Submit Request**
5. In the response, find the `local_key` field

**Alternative**: Use [tinytuya](https://github.com/jasonacox/tinytuya):

```bash
pip install tinytuya
python -m tinytuya wizard
```

Follow the wizard to scan your network and retrieve all device keys.

## Method 2: Using tinytuya (Easier)

### Installation

```bash
pip install tinytuya
```

### Setup Wizard

```bash
python -m tinytuya wizard
```

The wizard will:
1. Ask for your Tuya IoT API credentials
2. Download device list from cloud
3. Scan local network for devices
4. Output a JSON file with all Device IDs and Local Keys

### Example Output

```json
{
    "name": "WIFi_Meter",
    "id": "bf12345678abcdef12xyzw",
    "key": "A1b2C3d4E5f6G7h8",
    "ip": "192.168.1.100",
    "version": "3.5"
}
```

## Important Notes

### Local Key Changes

The Local Key can change if:
- Device is re-paired with the app
- Device is reset
- Device firmware is updated

If LocalTuya stops working, you may need to get a new Local Key.

### Keep Your Keys Safe

- Do NOT share your Local Key publicly
- Do NOT commit files containing your Local Key to public repositories

### Protocol Version

The PC321-W-TY uses **Protocol Version 3.5**. Make sure to select this when configuring LocalTuya.

## Troubleshooting

### Can't Find Device on Tuya IoT Platform

- Make sure the device is linked to Smart Life app
- Ensure you linked the correct Smart Life account
- Check the Data Center matches your app's region

### Local Key Doesn't Work

- Verify you copied the entire key (some characters may look similar)
- Try regenerating the key by re-pairing the device
- Check Protocol Version is set to 3.5

### API Access Denied

- Subscribe to the necessary APIs in your Tuya Cloud project
- Check your API quota hasn't been exceeded
