# wifi-heatmap

![Tests](https://img.shields.io/badge/tests-110%2B%20passing-brightgreen) ![Python](https://img.shields.io/badge/python-3.9%2B-blue) ![License](https://img.shields.io/badge/license-MIT-green) ![Platform](https://img.shields.io/badge/platform-macOS%20%7C%20Linux%20%7C%20Windows-lightgrey)

A WiFi coverage heatmap tool. Click points on a floor plan, walk around your house, and visualize signal strength, throughput, and coverage gaps.

Built with Python + Flask. No cloud, no accounts — everything runs locally.

<img width="1703" height="873" alt="wifi-heatmap screenshot" src="https://github.com/user-attachments/assets/ddcf6923-a343-4d0c-9fa0-1f96d72d286e" />

## Features

- **Click-to-measure** on any floor plan image — each click captures RSSI, BSSID, channel, PHY mode, Tx rate, noise floor
- **Multi-scan averaging** — takes 3 samples per measurement, picks the strongest (or detects mesh roaming)
- **4 heatmap modes** — RSSI, SNR, Tx Rate, Download Speed
- **Room labels** — draw room boundaries, get per-room signal analysis
- **Access point mapping** — mark where your routers are, link by BSSID (fuzzy MAC matching for mesh nodes)
- **Insights engine** — dead zones, weak points, AP coverage %, sticky-client detection, survey quality scoring
- **Snapshots & comparison** — save a survey, move your router, re-survey, compare with real numbers
- **Live signal monitor** — real-time RSSI, SNR, channel, Tx rate dashboard
- **Speed test** — optional Cloudflare download test at each point
- **Force roam** — WiFi off/on toggle for mesh networks with sticky clients
- **PNG export** — composited floor plan + heatmap + markers + legend
- **Remote scanning** — run `agent.py` on another machine, control the survey from your browser

## Requirements

- **macOS 14+** for local scanning (uses `wdutil info`)
- **Python 3.9+**
- Connected to the WiFi network you want to map
- For remote scanning: `agent.py` runs on macOS, Linux, or Windows (zero dependencies)

## Install

```bash
git clone https://github.com/ribaldorafael/wifi-heatmap.git
cd wifi-heatmap
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
```

## Run

```bash
./run.sh
# or: python3 app.py
```

Open http://localhost:5001 in your browser.

## How it works

1. **Authenticate** — enter your sudo password (local mode) or a remote agent IP
2. **Upload a floor plan** — any image (PNG, JPG, hand-drawn sketch)
3. **Draw rooms** — click two corners to define room boundaries
4. **Place access points** — mark where your routers are on the map
5. **Click to measure** — each click scans WiFi and drops a colored marker
6. **Toggle the heatmap** — switch between RSSI, SNR, Tx Rate, or Speed views
7. **Check insights** — the Insights tab auto-analyzes coverage, dead zones, and sticky-client behavior
8. **Save & compare** — save a snapshot, make changes, compare before/after

## Remote scanning

Run the agent on any machine (macOS, Linux, or Windows):

```bash
# Copy agent.py to the remote machine, then:
python3 agent.py
```

No pip install needed — pure stdlib. Enter the remote machine's IP on the auth screen of the main app.

## Architecture

```
app.py              Flask setup, AppState, blueprint registration
routes/
  survey.py         Points, APs, rooms, floorplan, heatmap
  snapshots.py      Snapshot CRUD, insights, comparison
  scan.py           Auth, measure, live scan, export
scanner.py          Scanner Protocol, WifiScanner, RemoteWifiScanner
storage.py          JSON persistence (atomic writes)
heatmap.py          numpy-only IDW interpolation + matplotlib rendering
insights.py         Analysis engine (pure Python, no Flask dependency)
export.py           PIL compositing for PNG export
agent.py            Standalone remote scan agent (cross-platform, zero deps)
static/             JS + CSS (vanilla, no framework)
templates/          Single-page HTML (Geist font, dark theme)
tests/              110+ unit + integration tests
data/               Survey data (gitignored, created on first use)
```

## Tests

```bash
python3 -m unittest discover -s tests -v
```

## Notes on macOS WiFi APIs

Modern macOS has progressively locked down WiFi data access:

- `airport -s` — **removed** in macOS 14.4
- `system_profiler SPAirPortDataType` — **redacts BSSIDs**
- `ioreg` for `IO80211BSSID` — returns **all zeros**
- `wdutil info` — **works fully** but requires sudo

We use `wdutil info`. The remote agent also supports Linux (`nmcli`/`iwconfig`) and Windows (`netsh`).

## License

MIT
