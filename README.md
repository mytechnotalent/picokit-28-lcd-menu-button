![picokit-28-lcd-menu-button](https://raw.githubusercontent.com/mytechnotalent/picokit-28-lcd-menu-button/main/picokit-28-lcd-menu-button.png)

<br>

## FREE Reverse Engineering Self-Study Course [HERE](https://github.com/mytechnotalent/reverse-engineering)
## FREE Embedded Hacking Course [HERE](https://github.com/mytechnotalent/Embedded-Hacking)

<br>

# PICOKIT-28 LCD MENU BUTTON

### Menu Navigation with the GP15 Button and Authenticated Heartbeat
#### Lesson 28 of the Picokit Series

<br>

***
**LEGAL DISCLAIMER:**
The information, tools, and code provided in this repository and course are strictly for educational, research, and defensive purposes only.

You are explicitly prohibited from using any materials contained herein to access, test, modify, or exploit any device, network, or system that you do not own 100% or for which you do not have explicit, documented, and legally binding authorization to interact with.

By using this repository and course, you acknowledge and agree that:

1. Any illegal, unauthorized, or malicious use of this information is solely your responsibility.
2. The author(s) and contributor(s) of this repository and course shall not be held liable for any damages, legal repercussions, criminal charges, or unauthorized actions resulting from the use, misuse, or abuse of the contents herein.
3. You will comply with all applicable local, state, national, and international laws regarding cybersecurity and computer fraud.

**IF YOU DO NOT AGREE WITH THESE TERMS, DO NOT USE THIS REPOSITORY AND COURSE.**
***

<br>
<br>

## Overview

The twenty-eighth Picokit lesson. The node shows a three item menu on the 1602
I2C LCD and steps the highlight with the GP15 push button, wrapping at the end
of the list. Every five seconds the node transmits an authenticated heartbeat
over LoRa to a Python gateway that logs and displays the selected menu item.
It turns a single button and a small display into a navigable local menu.

<br>

## What it teaches

- Debouncing a single tactile button into clean press edges.
- Advancing and wrapping a bounded menu index.
- Rendering the menu position and label to the 1602 LCD.
- Reporting the selected item in the authenticated heartbeat body.

<br>

## Hardware

| Peripheral | Pico 2 pin | Role |
| --- | --- | --- |
| Button | GP15 | menu navigation, active low |
| 1602 I2C LCD | GP2 SDA / GP3 SCL | menu display |
| Red / Yellow / Green | GP16 / GP18 / GP17 | menu position indicator |
| Onboard LED | GP25 | heartbeat, one blink per transmit |
| RYLR998 | GP8 TX / GP9 RX | LoRa heartbeat |
| Debug Probe | SWCLK/SWDIO/GND, GP0/GP1 | SWD and the console |

<br>

## How it works

The node runs `monitor_step` in a loop. Every 100 ms it polls the debounced
button and, on a fresh press, advances the highlighted item across START, SET,
and INFO, wrapping at the end. It renders `MENU n/3` and the bracketed label to
the 1602 LCD, and every 5 seconds it seals `{"n":28,"s":<seq>,"m":<item>}` with
the field key and sends it over LoRa. The gateway authenticates each frame and
only then parses it.

<br>

## Build and flash

```bash
cd firmware
cmake -S . -B build -G Ninja -DPICO_BOARD=pico2 -DPICO_PLATFORM=rp2350-arm-s
cmake --build build
openocd -f interface/cmsis-dap.cfg -f target/rp2350.cfg \
  -c "program build/picokit_28_lcd_menu_button.elf verify reset exit"
```

<br>

## Watch the node

Open the console at 115200 and reset:

```text
BOOT
I2C scan:
  found 0x27
=== PICOKIT-28 LCD MENU BUTTON // MENU + BUTTON + AUTHENTICATED HEARTBEAT ===
MENU 2 SET
MENU 3 INFO
MENU 1 START
RX from 0x0001, N bytes
```

<br>

## The gateway

```bash
cd gateway
python3 -m venv .venv && source .venv/bin/activate
pip install -r requirements.txt
python3 listen.py --port /dev/cu.usbserial-A50285BI --hub 0001 --network 18 --db gateway.db
```

It prints `OK node=28 rssi=...` per authenticated heartbeat. The terminal
dashboard `python3 tui.py --db gateway.db` and the web dashboard
`python3 web/app.py --db gateway.db` show the same rows.

<br>

## Verify

```bash
python3 .opencode/skill/embedded-c-standard/audit_c_standard.py
python3 .opencode/skill/embedded-python-standard/audit_python_standard.py
python3 .opencode/skill/iot-readme-standard/validate_readme.py
python3 .opencode/skill/iot-banner-standard/validate_banner.py
python3 scripts/run_tests.py
python3 scripts/check_coverage.py
```

<br>

# Next
[picokit-29-lcd-menu-remote](https://github.com/mytechnotalent/picokit-29-lcd-menu-remote)

<br>

# License
[MIT License](https://github.com/mytechnotalent/picokit-28-lcd-menu-button/blob/main/LICENSE)
