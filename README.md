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

## Setup Instructions

### 2. Configure WiFi Credentials
Edit these lines in the code:

```cpp
const char* WIFI_SSID = "YOUR_WIFI_SSID";
const char* WIFI_PASSWORD = "YOUR_WIFI_PASSWORD";

echo -n "YourNewPassword" | sha256sum

const char* ADMIN_PASSWORD_HASH = "your_new_hash_here";

smart_home_security/
├── smart_home_security.ino
└── README.md

