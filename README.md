# Gridiron Dashboard 🏈

**An ESPHome-powered NFL touchscreen dashboard for the ESP32-S3.**  
The project currently focuses on the **Kansas City Chiefs** and combines live team information, NFL news, international-game screens and a player roster view with a dynamically drawn Chiefs jersey.

> **Unofficial fan-made project.** This project is not affiliated with, endorsed by, or sponsored by the National Football League (NFL), the Kansas City Chiefs, or any other team, league, event, or organization.

---

## Project vision

The goal is to build a polished, always-on NFL command center for a small round touchscreen: part scoreboard, part team hub, part game-day companion.

The first milestone is a stable and feature-rich Chiefs dashboard. From there, the project is intended to grow into a configurable NFL dashboard that can support other teams and richer game, player and league data.

## Current project status

This is an **active prototype / work in progress**. The configuration already contains the screens and integrations listed below, but not every feature has been validated on the physical display yet. In particular, the roster/jersey work is being integrated and its ESPHome build still needs to be confirmed on the target hardware.

### Implemented in the current configuration

- ESP32-S3 configuration with 16 MB flash and octal PSRAM at 80 MHz.
- 480 × 480 display configuration with GT911 touch input.
- Wi-Fi, ESPHome Native API and OTA update support.
- SNTP clock using the `Europe/Berlin` time zone.
- Chiefs team overview, including record, division standing and next/previous game information.
- NFL news feed with three headlines and a separate news-detail view.
- International-game presentation pages for the Munich and Madrid games.
- Chiefs roster loaded locally from a compact player-data set embedded in the firmware (no large roster download at runtime).
- Jersey-style player screen drawn from display primitives (not a static jersey image), with player number, name, position, height, weight, college and roster status when provided by the API.
- Player navigation using horizontal swipes and on-screen previous/next controls.
- Periodic data refresh and display refresh logic.
- Night-mode / display wake and touch navigation logic.

**Important:** “Implemented in the configuration” does not mean every item has passed a full hardware test. See [Known limitations](#known-limitations).

## Hardware

| Component | Configuration |
|---|---|
| Main controller | ESP32-S3 |
| Flash | 16 MB |
| PSRAM | Octal, 80 MHz |
| Display | 480 × 480 pixels |
| Touch controller | GT911 |
| Framework | ESPHome with ESP-IDF |
| Time zone | `Europe/Berlin` |

The exact display module revision, pinout and wiring should be checked against the specific board in use. GPIO assignments can vary between board revisions.

## Software and data sources

The dashboard uses ESPHome's `http_request` integration to retrieve data from external services. The current configuration references:

| Purpose | Endpoint / source |
|---|---|
| Chiefs team overview | `https://nfl-fan-app.de/api/team-overview?team=KC` |
| Game situation | `https://nfl-fan-app.de/api/game-situation` |
| Chiefs roster | Local compact player data embedded via `chiefs_roster.h` |
| NFL headlines | ESPN site API: `https://site.api.espn.com/apis/site/v2/sports/football/nfl/news?limit=3` |

The project has also explored the service's team-stats endpoint (`/api/team-stats?team=KC`) for quarterback season statistics and recent-game yardage progression. The intended next step is to request player-specific live/season statistics only after the user opens a player profile, rather than fetching statistics for the entire roster in the background.

External APIs can change, become unavailable, impose rate limits or return incomplete data. The dashboard is not guaranteed to work if a provider changes its response format. Review each provider's terms and permissions before relying on or redistributing its data.

## Repository structure

```text
gridiron-dashboard/
├── chiefs-dashboard.yaml       # Main ESPHome configuration
├── chiefs_roster.h             # Compact roster data embedded at build time
├── chiefs-roster-compact.json  # Editable source for the embedded roster data
├── secrets.yaml.example        # Example Wi-Fi secrets (no real credentials)
├── README.md
├── .gitignore
└── images/
    └── nfl/                    # Local image assets; not tracked in this repository
        └── brand/              # Local league / event branding assets
```

The exact local asset folder may contain additional files. The current YAML references local team and event graphics under `images/nfl/` and `images/nfl/brand/`.

### Files intentionally excluded from Git

- `secrets.yaml` and all credentials.
- `.venv/` and other local Python environments.
- `.esphome/` and generated build output.
- `backup/` and local backups.
- `images/nfl/` and local NFL/team/event graphics.

The repository's `.gitignore` is intended to keep these local files out of commits. Check `git status` before every push and never force-add secrets or unlicensed artwork.

## Requirements

- Compatible ESP32-S3 board with 480 × 480 display and GT911 touch controller.
- USB data cable.
- Windows, macOS or Linux computer.
- Python 3.
- ESPHome version compatible with the configuration.
- Wi-Fi network.
- All image files referenced by the YAML, placed at the exact local paths expected by the configuration.

## Setup

### 1. Clone the repository

```bash
git clone https://github.com/Schildizz/gridiron-dashboard.git
cd gridiron-dashboard
```

### 2. Create a Python environment and install ESPHome

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

### 3. Configure Wi-Fi secrets

Copy the example file:

**Windows PowerShell**

```powershell
Copy-Item secrets.yaml.example secrets.yaml
```

**macOS / Linux**

```bash
cp secrets.yaml.example secrets.yaml
```

Edit `secrets.yaml` with your own Wi-Fi credentials. The configuration expects:

```yaml
wifi_ssid: "YOUR_WIFI_NAME"
wifi_password: "YOUR_WIFI_PASSWORD"
```

Never commit `secrets.yaml`.

### 4. Add local image assets

The YAML loads local PNG graphics at build time. The repository does not distribute those files. Add the assets you are entitled to use under the paths referenced by `chiefs-dashboard.yaml`, including the required files under `images/nfl/` and `images/nfl/brand/`.

**The project will not compile if a referenced image file is missing.** Use only artwork you have permission to use, and do not assume that a publicly accessible logo is free to redistribute.

### 5. Validate and compile

```bash
python -m esphome config chiefs-dashboard.yaml
python -m esphome compile chiefs-dashboard.yaml
```

### 6. Upload to the ESP32-S3

Replace `COM7` with the serial port assigned to your board:

```bash
python -m esphome upload chiefs-dashboard.yaml --device COM7
```

On macOS or Linux, use the appropriate `/dev/...` device path.

### 7. View logs

```bash
python -m esphome logs chiefs-dashboard.yaml --device COM7
```

## Controls and navigation

The display uses touch gestures and on-screen controls. The exact navigation flow may evolve as pages are refined.

- Swipe horizontally to move through the dashboard / roster navigation.
- On the roster page, swipe left or right to select another player.
- Use the on-screen `PREV` and `NEXT` areas to change the selected player.
- Tap a news story to open its detail page.
- Touch input is also used for waking the display from its dimmed/off state.

## Roadmap

The following list captures the project's broader goals. Items are plans, not claims that the feature is already finished.

### Stability and hardware

- [ ] Complete a clean ESPHome compile with the current roster implementation.
- [ ] Test all pages, touch gestures and refresh behavior on the physical 480 × 480 display.
- [ ] Verify display backlight power-off / sleep behavior and reliable wake-up.
- [ ] Document the exact board revision, wiring, pinout and GPIO assignments.
- [ ] Review GPIO19 / GPIO20 use against the ESP32-S3 USB-Serial-JTAG interface and the actual board wiring.
- [ ] Improve memory use and resilience when APIs return large or malformed responses.

### Chiefs dashboard and live game center

- [ ] Refine the main dashboard layout and visual hierarchy.
- [ ] Add richer live-game information where the API provides it: possession, down and distance, quarter, game clock, current drive and win probability.
- [ ] Add scoring-play / play-by-play information if a reliable data source is available.
- [ ] Add on-demand player statistics: request the selected player's stats from the API only when their profile is opened / tapped.
- [ ] Expand team statistics, including quarterback season stats and recent-game trends.
- [ ] Add loading and error feedback while an individual player's statistics are being fetched.
- [ ] Improve loading, stale-data and API-error states.

### Roster and player experience

- [ ] Finish and validate the dynamic Chiefs jersey / roster page.
- [ ] Add a clear way to leave the roster page and return to the dashboard / previous screen.
- [ ] Define reliable back-navigation behavior from every roster subview and player profile.
- [ ] Add position filters (QB, RB, WR, TE, OL, DL, LB, CB, S and special teams).
- [ ] Add player headshots where permitted and technically practical.
- [ ] Add a richer player profile with available season statistics and player details.
- [ ] Improve navigation through large rosters and handle players without a jersey number or optional data.

### NFL coverage and international games

- [ ] Continue refining the Munich and Madrid international-game pages.
- [ ] Add the Mexico international-game page.
- [ ] Make international-game content easier to update each season.
- [ ] Improve league-wide news and game information.

### Long-term direction

- [ ] Add configurable team selection instead of hard-coding Kansas City (`KC`).
- [ ] Expand the dashboard toward all 32 NFL teams.
- [ ] Build a reusable team-data layer so team-specific pages can share the same UI.
- [ ] Consider additional dashboard pages / widgets for standings, schedules, player stats and game-day information.

## Known issues and limitations

- The project is under active development; the current configuration is a prototype.
- **Roster navigation trap:** after entering the roster view, the current navigation flow does not yet provide a reliable way to return to the previous dashboard screen. This is a known usability issue and is on the roadmap.
- **Player statistics are not yet loaded on demand:** the intended behavior is to call the player-statistics API only when a player is selected / their profile is opened. This has not yet been implemented.
- The local roster data is embedded in the firmware at build time. Updating the roster therefore requires updating the compact JSON source / generated header, recompiling and flashing the device.
- The local roster / jersey implementation still requires a successful compile and physical-device test.
- External API response formats and availability are outside this project's control.
- Some live-game details are goals only and may not yet be shown by the current display code.
- Player-specific statistics are planned as on-demand requests and are not yet part of the current roster interaction.
- The repository does not include the local image assets referenced by the YAML; these must be supplied separately.
- GPIO19 and GPIO20 can conflict with the ESP32-S3 USB-Serial-JTAG interface depending on board wiring. Review the warnings for your hardware before using those pins.
- Exact wiring and board-revision documentation is still to be completed.

## Branding, trademarks and licensing of assets

“NFL”, team names, event names, logos and other third-party marks are the property of their respective owners. This repository does not grant permission to use or redistribute those marks or graphics. The project is unofficial and independent.

Use only images, fonts, APIs and datasets in accordance with their licenses and terms. Check the terms of external data providers before using their services in a public or commercial project.

## Contributing

Ideas, bug reports and improvements are welcome. Before opening a pull request:

1. Keep secrets, local build folders and unlicensed artwork out of Git.
2. Describe the hardware and ESPHome version used for testing.
3. Include relevant compile logs for build issues.
4. Clearly distinguish tested behavior from planned functionality.

## License

No open-source license is currently provided. All rights are reserved by default. Unless a license is added, the repository does not grant permission to use, modify, distribute or sublicense its contents.

---

**Built for the love of football. Go Chiefs! ❤️💛**
