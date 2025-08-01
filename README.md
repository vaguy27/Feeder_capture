# Pet Feeding Automation System

This repository contains a Raspberry Pi-based automated pet feeding and containment system.

## Project Overview

This is a Raspberry Pi-based automated pet feeding and containment system that controls a servo motor for door mechanisms and smart feeders for pets named Trixie and Pickles. The system uses PIR motion sensors to detect pet presence and automate feeding sequences.

## Architecture

The codebase consists of standalone Python scripts that work together to create an automated pet management system:

### Core Components

- **Servo Control Scripts**: `120.py`, `90.py`, `close.py`, `open.py` - Control servo motor for door mechanism
  - Door servo (GPIO 17): Controls entry/exit door
- **Sensor Monitoring**: `check_entry.py` - Main control loop monitoring PIR sensors
- **Feeder Control**: `myfeeder2.py` - Controls smart feeders via TinyTuya cloud API

### Hardware Integration

- **GPIO Pins**:
  - Pin 17: Door servo motor
  - Pin 4: PIR motion sensor
- **External APIs**: TinyTuya cloud service for smart feeder control

#### GPIO Pin Layout
![Raspberry Pi GPIO Layout](RES/RaspberryPI-output.png)

#### Servo Housing and Actuator
![Servo Housing Actuator](RES/Servo_housing_Actuator.png)

#### Door Hasp Mechanism
![Door Hasp](RES/Door_Hasp.png)

### Control Flow

1. `check_entry.py` monitors PIR sensor for pet presence
2. When triggered, executes `120.py` to close door servo
3. Continues monitoring PIR sensor for pet presence in containment area
4. Triggers feeding sequence via `myfeeder2.py` for both pets
5. Opens door after delay using `open.py`

## Development Environment

- **Python Version**: 3.11 (virtual environment at `.venv/`)
- **Key Dependencies**: 
  - `RPi.GPIO` - Raspberry Pi GPIO control
  - `tinytuya` - Smart device cloud API
  - `python-dotenv` - Environment variable management

## Running Scripts

### Individual Servo Control
```bash
python 120.py     # Close door servo
python 90.py      # Alternative close (different angle)
python close.py   # Close door servo
python open.py    # Open door servo
```

### Main System
```bash
python check_entry.py  # Start main monitoring loop
```

### Manual Feeding
```bash
/home/metalo/src/.venv/bin/python myfeeder2.py trixie 3   # Feed Trixie 3 portions
/home/metalo/src/.venv/bin/python myfeeder2.py pickles 3 # Feed Pickles 3 portions
```

## File Dependencies

All scripts now use local file paths within the repository directory structure.

## Security Configuration

### Environment Variables Setup

1. **Install python-dotenv**: `pip install python-dotenv`
2. **Copy environment template**: `cp .env.example .env`
3. **Configure credentials** in `.env` with your actual TinyTuya API credentials:
   - `TINYTUYA_API_KEY` - Your TinyTuya API key
   - `TINYTUYA_API_SECRET` - Your TinyTuya API secret
   - `PICKLES_DEVICE_ID` - Device ID for Pickles' feeder
   - `TRIXIE_DEVICE_ID` - Device ID for Trixie's feeder

### Security Best Practices

- **Never commit `.env` file** - It's excluded in `.gitignore`
- **Use `.env.example`** for sharing configuration structure
- **Rotate API credentials** regularly
- **Restrict API permissions** to minimum required scope
- **Monitor API usage** for unauthorized access

### Files Containing Credentials

- `.env` - Contains actual secrets (never commit)
- `.env.example` - Template showing required variables (safe to commit)
- `myfeeder2.py` - Uses environment variables via `os.getenv()`

## Hardware Requirements

- Raspberry Pi with GPIO access
- One servo motor connected to pin 17
- PIR motion sensor on pin 4
- Network connectivity for TinyTuya API calls
- Smart pet feeders compatible with Tuya (recommended: [WOPET 6L Automatic Cat Feeder, WiFi Automatic Dog Feeder](https://a.co/d/iWvjJys))

### Physical Setup Images

#### Servo Positions
- ![90 Degree Position](RES/90py.png) - Door trap position allows entry but no exit.
- ![120 Degree Position](RES/120py.png) - Door locked position no entry or exit. When this happens the PIR sensor must have been triggered.

#### 3D Models
- Door catch mechanism: `RES/Trixie_catch.stl` and `RES/Trixie_catch.3mf`
- Servo housing with linear actuator: `RES/Servo-mnv-linear2.stl` and `RES/Servo-mnv-linear2.3mf`

#### Observations and Notes
Trixie, being a young female Chihuahua, would figure out a way to stay outside of the device, so I used the Tuya API for the pet feeder to only drop food when she is in the crate, and it would not drop my other dog Pickles' food until she was detected inside the crate. The routine starts at 12 noon and 6 PM. Thirty seconds after her food drops, Pickles' feeder drops his food. There are fail-safes for Trixie, where I set a cron job for an auto-release at a designated time after the food drops.
