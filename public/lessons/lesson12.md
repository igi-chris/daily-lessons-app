# Lesson 12: Sensor Platforms and Data Loggers

Sensors rarely work alone; they sit on platforms that provide **power, logging and telemetry**.

---

### Common platforms
| Platform | Power | Comms | Typical use |
|----------|-------|-------|-------------|
| River sonde (multi‑probe) | Battery + solar | 4G / LoRa | Regulatory sites |
| Pressure logger in borehole | Lithium cell | none | Groundwater trend |
| Smart buoy | Solar | Satellite | Lakes, estuaries |

### Data integrity
Store raw readings + calibration files. Use *redundant memory*: many loggers have dual flash. Clock drift of ±1 min month⁻¹ can desynchronise multi‑site studies — sync with GPS.

### Local storage vs cloud
Local SD card gives high‑freq backup; daily push to cloud allows dashboards. MQTT is popular for IoT; HTTP POST suits simpler setups.

---

**Quiz**  
List two advantages of push (sensor→server) over pull (server→sensor) data collection.

