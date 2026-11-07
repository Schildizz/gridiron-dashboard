# Gridiron Dashboard

An ESPHome-powered NFL touchscreen dashboard built around an ESP32-S3. The current prototype is focused on the Kansas City Chiefs, with the longer-term goal of supporting all 32 NFL teams.

> **Unofficial fan project.** This project is not affiliated with, endorsed by, or sponsored by the National Football League (NFL), the Kansas City Chiefs, or any other team or organization.

## Project status

This is an actively developed DIY project. The public repository contains the project configuration and setup documentation; it does not include NFL team logos, NFL branding assets, local secrets, or generated ESPHome build files.

Some setup steps and hardware details may change as the project develops. Check the known limitations below before building.

## Current hardware and software

Confirmed from the current ESPHome configuration and project setup:

- ESP32-S3
- 16 MB flash
- Octal PSRAM, configured at 80 MHz
- 480 × 480 touchscreen display
- GT911 touch controller
- ESPHome with the ESP-IDF framework
- Wi-Fi connectivity
- ESPHome native API and OTA update support
- SNTP time synchronization using the `Europe/Berlin` time zone
- Local PNG team images referenced by the ESPHome configuration

The exact display board revision and any additional wiring details should be verified against your hardware before assembly.

## Repository contents

```text
gridiron-dashboard/
├── chiefs-dashboard.yaml
├── .gitignore
├── README.md
└── secrets.yaml.example
```

The following items are intentionally not tracked:

- `secrets.yaml` and other credentials
- `.venv/` and other Python virtual environments
- `.esphome/` and generated build data
- `backup/`
- `images/nfl/` (NFL team and league graphics)

## Requirements

- ESP32-S3 board with compatible 480 × 480 display and GT911 touch controller
- USB data cable
- Windows, macOS, or Linux computer
- Python environment with ESPHome installed
- Wi-Fi network

The configuration currently uses ESPHome's ESP-IDF framework. Install a current ESPHome version compatible with the configuration.

## Getting started

1. Clone this repository:

   ```bash
   git clone https://github.com/YOUR_GITHUB_USERNAME/gridiron-dashboard.git
   cd gridiron-dashboard
   ```

2. Create a Python virtual environment and install ESPHome:

   **Windows PowerShell**

   ```powershell
   py -m venv .venv
   .\.venv\Scripts\Activate.ps1
   python -m pip install --upgrade pip
   pip install esphome
   ```

   **macOS / Linux**

   ```bash
   python3 -m venv .venv
   source .venv/bin/activate
   python -m pip install --upgrade pip
   pip install esphome
   ```

3. Create your local secrets file by copying the example:

   **Windows PowerShell**

   ```powershell
   Copy-Item secrets.yaml.example secrets.yaml
   ```

   **macOS / Linux**

   ```bash
   cp secrets.yaml.example secrets.yaml
   ```

4. Edit `secrets.yaml` and enter your own Wi-Fi network name and password. Do not commit this file.

5. Add the image assets required by your local configuration. The YAML references PNG files under `images/nfl/`, including team logos. Those files are not included in this repository. Only use image assets that you have the right to use. The configuration will not compile until all referenced image files are available at the expected paths.

6. Validate and compile:

   ```bash
   python -m esphome config chiefs-dashboard.yaml
   python -m esphome compile chiefs-dashboard.yaml
   ```

7. Connect the ESP32-S3 over USB and upload using the appropriate serial port:

   ```bash
   python -m esphome upload chiefs-dashboard.yaml --device COM7
   ```

   Replace `COM7` with the serial port assigned to your board. On macOS or Linux, use the appropriate `/dev/...` device path.

8. View device logs:

   ```bash
   python -m esphome logs chiefs-dashboard.yaml --device COM7
   ```

## Configuration and secrets

The ESPHome configuration expects these secret keys:

```yaml
wifi_ssid: "YOUR_WIFI_NAME"
wifi_password: "YOUR_WIFI_PASSWORD"
```

Keep the real values in `secrets.yaml` only. Never publish Wi-Fi credentials, API keys, encryption keys, device passwords, or personal tokens.

## Roadmap

- [x] Initial Kansas City Chiefs-focused ESPHome prototype
- [x] ESP32-S3 configuration with ESP-IDF
- [x] Wi-Fi, native API, and OTA configuration
- [x] SNTP time synchronization
- [x] Local team-image references in the ESPHome configuration
- [ ] Improve display backlight power-off behavior
- [ ] Add the Mexico international game to the dashboard
- [ ] Document complete wiring and exact board model
- [ ] Make team selection configurable
- [ ] Expand and test support for all 32 NFL teams

## Known limitations

- Display backlight power-off behavior is not yet working reliably in the current prototype.
- The Mexico international game is not yet implemented.
- The repository does not include the NFL image assets referenced by the YAML. You must supply compatible assets locally before compiling.
- This project is under active development; configuration details and features may change.

## Images, trademarks, and data sources

NFL, team, event, and other third-party names, logos, and marks belong to their respective owners. No NFL or team logo files are redistributed in this repository. The unofficial-project disclaimer does not grant permission to use third-party trademarks, artwork, APIs, or data.

Check the terms and permissions of any external data source or image asset you choose to use. Do not assume that publicly accessible assets or API endpoints are licensed for redistribution.

## Contributions

Suggestions, bug reports, and improvements are welcome. Please do not submit copyrighted logos, credentials, private configuration files, or other material you do not have permission to distribute.

## License

No license is currently granted for this repository. Unless the repository owner adds a license, the default copyright rules apply and reuse, modification, and redistribution are not automatically permitted.
