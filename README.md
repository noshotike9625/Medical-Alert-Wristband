# Medical-Alert-Wristband
A wearable medication reminder device for elderly patients with hypertension. The device vibrates at a scheduled time to prompt the user to take their medication. A single button press confirms the dose. If no response is received within the response window, a caregiver is automatically notified via IFTTT webhook. Built for ENGR 107 003 Spring GMU

## Team — Nexus (Group 3)

| Name | Role |
|------|------|
| Eyobed Ayele | Hardware lead, enclosure design,breadboard & perfboard prototyping stakeholder definition |
| Asrael | Firmware, Final Code, TPMs |
| Isaac Bogdewic | PDR/CDR reports, scope, integration & bug fixing |
| Mohammed | Requirements, parts procurement |

---

## Hardware

| Component | Part | Pin |
|-----------|------|-----|
| Microcontroller | Arduino MKR WiFi 1010 | — |
| RTC Module | DS3231 | SDA→A4, SCL→A5 |
| Vibration Motor | 10mm coin motor, 3V | D5 (via 2N3904 transistor) |
| Pushbutton | 6mm tactile switch | D2 (INPUT_PULLUP) |
| Battery | 3.7V Li-Po, 500–800 mAh | — |
| Charger | TP4056 module | — |

---

## Setup

### 1. Install Arduino libraries
In the Arduino IDE go to Sketch → Include Library → Manage Libraries and install:
- WiFiNINA
- RTClib (by Adafruit)

### 2. Configure credentials
Copy secrets.h.example to a new file called secrets.h and fill in your values:

#define WIFI_SSID "Your_Network_Name"
#define WIFI_PASS "Your_Password"
#define IFTTT_KEY "Your_IFTTT_Key"

secrets.h is listed in .gitignore and will never be uploaded to GitHub.

### 3. Set your alarm time
In src/main.ino update these lines:

const int ALARM_HOUR   = 13;  // 24-hour format (13 = 1:00 PM)
const int ALARM_MINUTE = 0;

### 4. Upload — two-phase process

Phase 1 — Set the RTC clock:
- Set SET_RTC_MODE = true
- Upload the sketch
- Open Serial Monitor at 115200 baud and verify the time is correct

Phase 2 — Normal operation:
- Set SET_RTC_MODE = false
- Upload again
- The device will now run normally and only re-sync the RTC if it loses power

---

## How It Works

RTC tick → compare with ALARM_HOUR:ALARM_MINUTE
  → match: motor pulses ON/OFF + response timer starts
    → button pressed within window : dose acknowledged
    → timer expires with no press  : WiFi → IFTTT POST → caregiver notified
  → new calendar day: all daily flags reset

The motor pulses in a non-blocking pattern using millis() — no delay() in the main loop.

---

## Key Parameters

| Constant | Default | Description |
|----------|---------|-------------|
| ALARM_HOUR | 13 | Hour to trigger alert (24h) |
| ALARM_MINUTE | 0 | Minute to trigger alert |
| RESPONSE_WINDOW_MS | 5000 | Milliseconds user has to press button |
| BUZZ_ON_MS | 200 | Motor ON duration per pulse |
| BUZZ_OFF_MS | 150 | Motor OFF duration per pulse |

---

## KPPs and TPMs

| Measure | Threshold | Target |
|---------|-----------|--------|
| Alert reliability | 100% trigger rate | 100% over 7-day test |
| Alert timing accuracy | Within 60 seconds | Within 15 seconds |
| Battery life | 5 days minimum | 7 days |
| Device weight | 150g maximum | 80g |
| Vibration frequency | 400–525 Hz | 480 Hz |

---

## Project Structure

medical-alert-wristband/
├── README.md
├── .gitignore
├── LICENSE
├── secrets.h.example
├── src/
│   └── main.ino
└── docs/
    ├── PDR.pdf
    ├── CDR.pdf
    └── final_presentation.pdf

---

## References

- Ruksakulpiwat S, et al. Medication adherence in hypertension. National Library of Medicine, PMC11088410. https://pmc.ncbi.nlm.nih.gov/articles/PMC11088410/
- Arduino MKR WiFi 1010 — https://docs.arduino.cc/hardware/mkr-wifi-1010
- Adafruit RTClib — https://github.com/adafruit/RTClib
- IFTTT Maker Webhooks — https://ifttt.com/maker_webhooks

---

## License

MIT License — see LICENSE for details.
