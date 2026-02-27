# Arvan Radar API

A lightweight Python project for fetching internet monitoring data from **ArvanCloud Radar** and visualizing ISP/datacenter quality over time.

This repository provides:
- Async data collection from Arvan Radar endpoints
- A simple API wrapper (`ArvanRadar`) for requesting one or multiple datacenters
- Plot generation utilities (`RadarPlot`) with two chart styles
- Raw data + image output integration for automation pipelines/bots

---

## Features

- **Asynchronous HTTP requests** via `aiohttp` for efficient multi-datacenter fetches
- **Batch querying** of one or more ISPs in a single call
- **Time targeting** support using `date` and `time` query parameters
- **Visualization utilities** based on `matplotlib` + `numpy`
- **In-memory image output** (PNG bytes) suitable for APIs, bots, or file storage

---

## Project Structure

```text
.
├── arvanApi.py      # API client and async request orchestration
├── extraData.py     # Datacenter key mapping + base API URL format
├── plot.py          # Plot generation logic (two plotting styles)
└── requirements.txt # Python dependencies
```

---

## Installation

### 1) Clone the repository

```bash
git clone https://github.com/AmirHBuilds/Arvan-Radar-Api
cd Arvan-Radar-Api
```

### 2) Create and activate a virtual environment (recommended)

```bash
python -m venv .venv
source .venv/bin/activate
```

### 3) Install dependencies

```bash
pip install -r requirements.txt
```

---

## Quick Start

```python
from arvanApi import ArvanRadar
from plot import RadarPlot

# Create API client
radar = ArvanRadar()

# Fetch data for selected datacenters/ISPs
# Available keys are in extraData.py -> datacenter_keys
data = radar.get_data(
    'Hamrah_aval',
    'Mobin_net',
    'Irancell',
    'Afranet'
)

# Build chart
plotter = RadarPlot(data)
image_bytes = plotter.make_plot_2()  # or make_plot_1()

# Save image to file
with open('radar.png', 'wb') as f:
    f.write(image_bytes)
```

---

## Supported Datacenter Keys

Default keys (from `extraData.py`):

- `Hamrah_aval`
- `Mobin_net`
- `Irancell`
- `Afranet`
- `Pars_online`
- `Host_iran`
- `Tehran_1`
- `Tehran_2`
- `Tehran_3`

You can also replace the mapping at runtime:

```python
radar = ArvanRadar()
radar.set_request_address_list({
    'CustomISP': 'custom-isp-key'
})
```

---

## API Usage

### `ArvanRadar.get_data(*site_name)`
Fetches monitoring data for the provided site/datacenter names.

- **Input**: one or more keys from `datacenter_keys`
- **Output**: list of tuples in this shape:
  - `[(site_name, response_json), ...]`

### `ArvanRadar.set_time(date, time)`
Appends date/time filters to query URLs.

Example:

```python
radar.set_time('2024-05-08', '12:00')
```

> Note: By default, data is fetched without explicit date/time filters unless `set_time(...)` is called.

### `RadarPlot(data, start_time=None)`
Creates a plotting object from fetched data.

- `make_plot_1()` → stacked bar style (more detailed)
- `make_plot_2()` → line chart style (lighter)

Both methods return **PNG bytes**.

---

## Typical Workflow

1. Instantiate `ArvanRadar`
2. Request one or more datacenters with `get_data(...)`
3. Pass data into `RadarPlot`
4. Render with `make_plot_1()` or `make_plot_2()`
5. Save bytes to disk or forward to another service

---

## Error Handling Notes

- Request connection errors in `make_request(...)` currently return `None`.
- If the HTTP session is missing, a custom `SessionError` is raised.
- `asyncio.gather(..., return_exceptions=True)` is used, so individual request failures may appear in results.

When integrating in production, validate each response before plotting.

---

## Development Notes

- Python 3.10+ is recommended.
- The codebase is simple and script-friendly, suitable for:
  - Internal monitoring dashboards
  - Telegram/Discord bots
  - Periodic reporting scripts (cron jobs)

---

## License

No explicit license is currently defined in this repository.
If you plan to distribute or publish this project, add a `LICENSE` file.

---

## Acknowledgments

Data source: [ArvanCloud Radar](https://radar.arvancloud.ir/)
