# Lesson 15: Telemetry and IoT Protocols

Getting data off‑site fast is crucial for incident response.

---

### Options
| Protocol | Range | Power | Notes |
|----------|-------|-------|-------|
| 4G/5G | Global | High / mains | Bandwidth for images |
| NB‑IoT | 10 km | Low | Good penetration |
| LoRaWAN | 2–15 km | Very low | Community gateways |
| Satellite (Iridium) | Anywhere | High | Low message size |

MQTT over TCP/IP offers light overhead; HTTP(S) is easier to debug. Always buffer locally in case of network outage.

### Security
TLS, rotating API keys, and physical tamper seals. In 2023, hackers altered a US reservoir’s pH sensor over telnet (!). Principle: **no open ports**.

---

**Quiz**  
Explain why a pressure logger in a deep rural well might use NB‑IoT rather than 4G.

