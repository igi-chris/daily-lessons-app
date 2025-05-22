# Lesson 16: Data Management and Visualization

A sensor network is only as useful as the insights you draw from its data.

---

### Storage
Time‑series databases (InfluxDB, TimescaleDB) handle millions of points efficiently. Store metadata: sensor ID, calibration dates, location (lat, long, depth).

### QC and aggregation
Run nightly scripts:  
* De‑spike  
* Gap‑fill (e.g. linear interpolation < 30 min gap)  
* Generate daily means, maxima, 95th percentiles.

### Dashboards
Grafana or ThingsBoard can map gauges, plot multi‑axis charts (stage vs turbidity), and trigger alerts (e‑mail, webhook) when thresholds exceed.

### Storytelling
Combine plots: rainfall vs flow vs nitrate spikes to show farm runoff impact. Public dashboards boost transparency (e.g. Thames Water’s live CSO outfall map).

---

**Try this**  
Sketch a simple dashboard layout showing pH, DO, level and a WQI gauge.

