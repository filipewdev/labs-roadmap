# Lab 24 — Arduino + MQTT Sensor Pipeline

**Goal**: Read temperature and humidity from a real sensor, publish via MQTT,
persist to PostgreSQL, visualize in Grafana. The full IoT data pipeline.

**Time box**: 8 hours

**Hardware**: Arduino Uno/Nano + DHT22 sensor (~R$15) + breadboard + wires

**Language**: C++ (Arduino) + Python (bridge) + TypeScript (subscriber)

**Done when**:
- [ ] DHT22 wired correctly and reading temperature + humidity
- [ ] Arduino publishes JSON readings over serial every 10 seconds
- [ ] Python bridge reads serial, publishes to MQTT (Mosquitto)
- [ ] TypeScript subscriber persists readings to PostgreSQL
- [ ] Grafana dashboard shows temperature and humidity over time
- [ ] Alert fires when temperature exceeds a threshold
- [ ] Open-Meteo API shows official Brasília temperature alongside your sensor reading

**Key concepts**:
- C++ setup/loop model (Arduino's execution model)
- Serial communication between Arduino and a host computer
- MQTT pub/sub protocol (why it's better than HTTP for sensors)
- `retain` flag: new subscribers see the last-known value immediately
- BRIN indexes in PostgreSQL for time-series data

**Testing requirement**: Python bridge unit tests using a mock serial port. TypeScript subscriber tests with a mock MQTT client. The Arduino firmware cannot be unit-tested — this is a real embedded development constraint.

---
