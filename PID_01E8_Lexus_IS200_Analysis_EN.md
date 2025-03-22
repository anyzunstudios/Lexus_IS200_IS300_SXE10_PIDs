# Analysis and Findings of PID 01E8 in the Lexus IS200 (2003, 1G-FE, Manual Transmission)

**Date:** 2025-03-22

## 📘 Introduction

This document compiles real-world discoveries using an ELM327 connected to a 2003 Lexus IS200 with a 1G-FE engine and manual transmission, focusing on the analysis of the non-standard PID `01E8`.

This PID returns a data byte that encodes the status of:

- Accelerator pedal
- Brake pedal
- A/C status
- Lights or rear window/mirror defogger

---

## 🧠 Bit-by-Bit Interpretation of PID 01E8 First Data Byte

| Bit | Mask   | Meaning                                       |
|-----|--------|-----------------------------------------------|
| 6   | 0x40   | Accelerator at idle (not pressed)             |
| 5   | 0x20   | A/C active                                    |
| 3   | 0x08   | Lights or defogger active                     |
| 2   | 0x04   | Brake pedal pressed                           |

> ⚠️ Bits 0, 1, 4, and 7 showed no active behavior during tests.

---

## 🔍 Nibble-Based Interpretation

| Nibble | Value | Meaning                                |
|--------|--------|----------------------------------------|
| High   | 0x0X   | Accelerator pedal pressed              |
| High   | 0x4X   | Accelerator at idle                    |
| Low    | 0xX0   | Brake not pressed                      |
| Low    | 0xX4   | Brake pressed                          |
| Low    | 0xX8   | Lights or defogger active              |

---

## 📊 Common Observed Combinations

| Byte  | Description                                           |
|--------|-------------------------------------------------------|
| 0x00   | Only accelerator pressed                              |
| 0x20   | Accelerator pressed + A/C active                      |
| 0x40   | Accelerator idle only                                 |
| 0x60   | Accelerator idle + A/C active                         |
| 0x04   | Accelerator + brake pressed                           |
| 0x24   | Accelerator + brake + A/C active                      |
| 0x44   | Brake only + accelerator idle                         |
| 0x64   | Brake + A/C + accelerator idle                        |
| 0x48   | Lights/defogger + accelerator idle                    |
| 0x68   | Lights/defogger + A/C + accelerator idle              |
| 0x2C   | Accelerator + brake + A/C + lights/defogger           |

---

## 💻 Arduino Code Example

```cpp
bool AcceleratorPressed = false;
bool AcceleratorIdle = false;
bool BrakePressed = false;
bool ACActive = false;
bool LightsOrDefoggerON = false;

void updateStates(byte state) {
  AcceleratorIdle = state & 0x40;
  AcceleratorPressed = !AcceleratorIdle;
  ACActive = state & 0x20;
  LightsOrDefoggerON = state & 0x08;
  BrakePressed = state & 0x04;
}
```

---

## 📌 Additional Notes

- This analysis was performed with the engine off.
- The ELM327 was configured with ISO9141-2 protocol (`AT SP 3`) and queried using command `01E8`.
- This behavior may apply to other Toyota/Lexus models using KWP2000.

---

**Author**: Julio J. Baena  
**Vehicle source**: Lexus IS200 2003, 1G-FE, manual transmission
