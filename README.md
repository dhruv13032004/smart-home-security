# Smart Home Security System - ESP32

## Overview

A secure smart home system with comprehensive cybersecurity features including authentication, rate limiting, intrusion detection, and security event logging.

## Components Used

| Component | Quantity | Purpose |
|-----------|----------|---------|
| ESP32 | 1 | Main controller |
| IR Sensor | 1 | Obstacle/Proximity detection |
| LDR Sensor | 1 | Light level monitoring |
| PIR Sensor | 1 | Motion detection (security) |
| DHT11/22 | 1 | Temperature & Humidity |
| HC-SR04 Ultrasonic | 1 | Distance/Presence detection |
| RC522 RFID | 1 | Access control |
| 4-Channel Relay | 1 | Appliance control |
| Breadboard | 1 | Prototyping |

## Wiring Diagram

                                ESP32 PINOUT
                       ┌─────────────────────────┐
                       │                         │
                  3V3 ─┤ 3V3              VIN   ├─ 5V
                  GND ─┤ GND              GND   ├─ GND
                       │                         │
       IR Sensor OUT ──┤ GPIO34 (ADC)    GPIO19 ├── Relay 4 IN
      LDR Sensor OUT ──┤ GPIO35 (ADC)    GPIO18 ├── Relay 3 IN
      PIR Sensor OUT ──┤ GPIO27          GPIO17 ├── Relay 2 IN
          DHT11 DATA ──┤ GPIO26          GPIO16 ├── Relay 1 IN
   Ultrasonic TRIG   ──┤ GPIO25          GPIO5  ├── RC522 SDA/SS
   Ultrasonic ECHO   ──┤ GPIO33          GPIO4  ├── RC522 RST
                       │                         │
                       │ GPIO23 ─── RC522 MOSI  │
                       │ GPIO19 ─── RC522 MISO  │
                       │ GPIO18 ─── RC522 SCK   │
                       │                         │
                       └─────────────────────────┘



COMPONENT WIRING DETAILS: ═════════════════════════

┌──────────────────┐ │ IR SENSOR │ ├──────────────────┤ │ VCC ──────► 3.3V │ │ GND ──────► GND │ │ OUT ──────► GPIO34│ └──────────────────┘

┌──────────────────┐ │ LDR SENSOR │ ├──────────────────┤ │ VCC ──────► 3.3V │ │ GND ──────► GND │ │ OUT ──────► GPIO35│ └──────────────────┘ (Use voltage divider with 10K resistor if using raw LDR)

┌──────────────────┐ │ PIR SENSOR │ ├──────────────────┤ │ VCC ──────► 5V │ │ GND ──────► GND │ │ OUT ──────► GPIO27│ └──────────────────┘

┌──────────────────┐ │ DHT11/22 │ ├──────────────────┤ │ VCC ──────► 3.3V │ │ GND ──────► GND │ │ DATA ─────► GPIO26│ └──────────────────┘ (Add 10K pull-up resistor between DATA and VCC)

┌──────────────────┐ │ HC-SR04 │ ├──────────────────┤ │ VCC ──────► 5V │ │ GND ──────► GND │ │ TRIG ─────► GPIO25│ │ ECHO ─────► GPIO33│ └──────────────────┘ (Use voltage divider on ECHO: 1K + 2K to bring 5V to 3.3V)

┌──────────────────────┐ │ RC522 RFID │ ├──────────────────────┤ │ 3.3V ─────► 3.3V │ │ GND ──────► GND │ │ RST ──────► GPIO4 │ │ SDA/SS ───► GPIO5 │ │ MOSI ─────► GPIO23 │ │ MISO ─────► GPIO19 │ │ SCK ──────► GPIO18 │ │ IRQ ──────► NC │ └──────────────────────┘

┌──────────────────────┐ │ 4-CHANNEL RELAY │ ├──────────────────────┤ │ VCC ──────► 5V │ │ GND ──────► GND │ │ IN1 ──────► GPIO16 │ │ IN2 ──────► GPIO17 │ │ IN3 ──────► GPIO18* │ │ IN4 ──────► GPIO19* │ └──────────────────────┘ *Note: GPIO18/19 conflict with SPI. If using all 4 relays, reassign to GPIO21, GPIO22

BREADBOARD LAYOUT (Simplified): ═══════════════════════════════




 +5V Rail ════════════════════════════════════════
GND Rail ════════════════════════════════════════

┌─────────┐  ┌─────────┐  ┌─────────┐  ┌─────────┐
│   IR    │  │   LDR   │  │   PIR   │  │  DHT11  │
└────┬────┘  └────┬────┘  └────┬────┘  └────┬────┘
     │            │            │            │
GPIO34       GPIO35       GPIO27       GPIO26

┌─────────────┐     ┌─────────────────────────┐
│  HC-SR04    │     │       RC522 RFID        │
│ TRIG  ECHO  │     │  RST SDA MOSI MISO SCK  │
└──┬─────┬────┘     └───┬───┬───┬────┬───┬────┘
   │     │              │   │   │    │   │
GPIO25 GPIO33       GPIO4 GPIO5 23   19  18

┌─────────────────────────────────────────────┐
│              4-CHANNEL RELAY                │
│    IN1    IN2    IN3    IN4                 │
└─────┬──────┬──────┬──────┬─────────────────┘
      │      │      │      │
   GPIO16 GPIO17 GPIO18 GPIO19


   
## Security Features

### 1. Authentication System
- SHA-256 password hashing
- Session-based authentication with tokens
- HTTPOnly cookies with SameSite=Strict
- IP-bound sessions (prevents session hijacking)
- Session timeout after 30 minutes of inactivity

### 2. Brute Force Protection
- Maximum 5 login attempts before lockout
- 5-minute IP lockout after failed attempts
- All failed attempts are logged with source IP
- Lockout status tracked per IP address

### 3. Rate Limiting
- Maximum 60 requests per minute per IP
- Prevents DoS attacks
- Blocked requests logged and counted

### 4. Input Validation
- Username limited to 32 characters
- Password limited to 64 characters
- RFID UID validation (8 hex characters)
- Card names limited to 20 characters
- HTML escaping to prevent XSS attacks

### 5. Security Headers
- X-Content-Type-Options: nosniff
- X-Frame-Options: DENY
- X-XSS-Protection: 1; mode=block
- Cache-Control: no-store, no-cache

### 6. Comprehensive Logging
- All security events logged with timestamp
- Source IP recorded for every event
- Severity levels: INFO, WARNING, CRITICAL
- Last 100 events stored in memory
- Events include:
  - Login attempts (success/failure)
  - Session creation/destruction
  - Rate limit violations
  - RFID access attempts
  - Intrusion detections
  - Relay state changes
  - System events

### 7. Intrusion Detection
- PIR motion detection
- Proximity alerts (ultrasonic < 50cm)
- Consecutive motion detection threshold
- Cooldown period to prevent alert spam
- Arm/Disarm functionality

### 8. Access Control
- RFID card whitelist management
- Add/Remove/Enable/Disable cards
- Cards stored in non-volatile memory
- Unauthorized card attempts logged

## Web Interface Pages

| Page | URL | Description |
|------|-----|-------------|
| Dashboard | `/` | Real-time sensor data, relay control |
| Login | `/login` | Authentication page |
| Security Logs | `/logs` | View all security events |
| RFID Management | `/rfid` | Manage authorized cards |
| Statistics | `/stats` | System stats and metrics |
| Logout | `/logout` | End session |

## API Endpoints

| Endpoint | Method | Description |
|----------|--------|-------------|
| `/api/relay?n=1-4` | POST | Toggle relay |
| `/api/arm` | POST | Toggle security arm |
| `/api/clear` | POST | Clear intrusion alert |
| `/api/sensors` | GET | Get sensor data JSON |

## Setup Instructions

### 1. Install Required Libraries
In Arduino IDE, install these libraries:
- ESP32 board support (by Espressif)
- MFRC522 (by GithubCommunity)
- DHT sensor library (by Adafruit)
- Adafruit Unified Sensor

### 2. Configure WiFi Credentials
Edit these lines in the code:
```cpp
const char* WIFI_SSID = "YOUR_WIFI_SSID";
const char* WIFI_PASSWORD = "YOUR_WIFI_PASSWORD";

3. Change Default Password
The default password is "admin". Generate a new SHA-256 hash:

echo -n "YourNewPassword" | sha256sum

Replace the hash in:
const char* ADMIN_PASSWORD_HASH = "your_new_hash_here";

4. Upload Code
 1.Connect ESP32 via USB
 2.Select board: "ESP32 Dev Module"
 3.Select correct COM port
 4.Click Upload

5. First Access
 1.Open Serial Monitor (115200 baud)
 2.Note the IP address displayed
 3.Open browser and navigate to that IP
 4.Login with admin credentials

Security Recommendations

CRITICAL - DO IMMEDIATELY:
 1.Change default password before connecting to network
 2.Use strong password (12+ characters, mixed case, numbers, symbols)
 3.Secure your WiFi network with WPA3 if possible

RECOMMENDED:
 1.Use a dedicated IoT VLAN/network
 2.Configure router firewall rules
 3.Disable remote access if not needed
 4.Regularly check security logs
 5.Keep only necessary RFID cards active
 6.Position sensors to minimize false alarms

LIMITATIONS:
-No HTTPS (ESP32 has limited TLS support)
-Single admin user only
-Session stored in memory (lost on reboot)
-100 log entries maximum

Troubleshooting
Issue	Solution
Can't connect to WiFi	Check credentials, ensure 2.4GHz network
RFID not reading	Check wiring, ensure 3.3V power
DHT reading NaN	Add pull-up resistor, check wiring
Relay not switching	Check if active-LOW, verify 5V supply
429 Too Many Requests	Wait 1 minute (rate limit)
Locked out	Wait 5 minutes or restart ESP32

smart_home_security/
├── smart_home_security.ino    # Main code
└── README.md                  # This file
