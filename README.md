# 🐦 Zerguz Canary System

**Zerguz** is a Python-based local Canary Token infrastructure built on Cyber Deception principles. It enables a SOC analyst or blue team member to detect unauthorized access, lateral movement, or insider threats on their network in real time, with **zero false positives**.

The system generates a fake Word document (`.doc`) with an attractive, believable filename. The moment this document is opened, an invisible tracking pixel embedded inside it silently sends an HTTP request to the Zerguz server. This request instantly reveals the attacker's **IP address**, **User-Agent string**, and **timestamp**, and automatically formats the event as **CEF (Common Event Format)**, ready to feed directly into a SIEM.

---

## 🎯 Why Is This Critical?

Under normal circumstances, no one should ever open this document. That means **every single request** hitting the `/track/` endpoint is a definitive indicator of a breach:

- ✅ Zero false positives — the token only fires on unauthorized access
- ✅ CEF-formatted output integrates directly with enterprise SIEMs such as Splunk, QRadar, and ArcSight
- ✅ A completely passive, non-malicious detection method — no payload is delivered to the attacker's machine
- ✅ The document renders cleanly in Microsoft Word without triggering any warnings, so it raises no suspicion

---

## 🗂️ Project Structure

```
zerguz/
├── zerguz_server.py       # Flask-based canary listener server
├── zerguz_generator.py    # Fake .doc canary document generator
├── zerguz_siem.log        # (auto-generated) CEF-formatted alert log
└── README.md
```

---

## ⚙️ Requirements

- Python 3.8+
- Flask

```bash
pip install flask
```

---

## 🚀 Setup and Usage

### 1. Start the server

```bash
python3 zerguz_server.py
```

The server starts listening on `0.0.0.0:5000` and prints the following banner to the terminal:

```
==============================================================================
                     ZERGUZ CANARY SYSTEM - SERVER ONLINE
==============================================================================
[*] Listening on: 0.0.0.0:5000
[*] Endpoint: /track/<doc_name>
[*] SIEM Log File: /path/to/zerguz_siem.log
==============================================================================
```

### 2. Generate the canary document

In a separate terminal:

```bash
python3 zerguz_generator.py
```

This automatically detects the machine's local network IP (no manual configuration needed) and produces a file named `2026_Salary_Raises_and_Bonus_List.doc` with a hidden tracking pixel embedded inside it.

```
==============================================================================
                           ZERGUZ CANARY GENERATOR
==============================================================================
[+] Document successfully generated : 2026_Salary_Raises_and_Bonus_List.doc
[+] Auto-detected local IP           : 192.168.1.35
[+] Embedded tracking URL            : http://192.168.1.35:5000/track/zerguz_salary_list
[+] Server port                      : 5000
==============================================================================
```

### 3. Place the document

Drop the generated `.doc` file into a shared folder, desktop, or test machine where you want detection coverage.

### 4. Trigger the alert

When the document is opened in Microsoft Word (or for a quick manual test, by visiting `http://<IP>:5000/track/zerguz_salary_list` directly in a browser), the server terminal instantly fires an alert:

```
##############################################################################
#                   ZERGUZ CANARY DETECTOR - CRITICAL ALERT                 #
##############################################################################
[!] STATUS         : CANARY TOKEN TRIGGERED
[!] TIMESTAMP      : 2026-07-21 14:32:10
[!] SOURCE IP      : 192.168.1.87
[!] USER AGENT     : Mozilla/5.0 (Windows NT 10.0; Win64; x64)
[!] DOCUMENT NAME  : zerguz_salary_list
[!] REQUEST METHOD : GET
------------------------------------------------------------------------------
[SIEM] CEF FORMATTED EVENT:
CEF:0|Zerguz|CanarySystem|1.0|100|Canary Token Triggered|10|src=192.168.1.87 ...
##############################################################################
```

The same event is also automatically appended to `zerguz_siem.log`:

```bash
cat zerguz_siem.log
```

---

## 🧩 Architecture Details

| Component | Description |
|---|---|
| `zerguz_server.py` | Serves a `/track/<doc_name>` endpoint via Flask, captures incoming requests, converts them to CEF, logs them, and returns a transparent 1x1 PNG |
| `zerguz_generator.py` | Generates an HTML-based, Word-compatible `.doc` file; auto-detects the machine's real local IP using `socket` |
| CEF Format | Follows the `CEF:0|Zerguz|CanarySystem|1.0|100|Canary Token Triggered|10|src=...` structure, aligned with enterprise SIEM standards |
| Stealth | The `<img>` tag is fully hidden using `display:none`, `width=1`, `height=1` — invisible when the document is opened in Word |

---

## ⚠️ Disclaimer

This project is intended **solely for educational purposes, portfolio demonstration, and authorized security testing**. Using it outside environments you own or have explicit written authorization to test may carry legal liability.

---

## 📌 Tags

`#CyberDeception` `#CanaryToken` `#BlueTeam` `#SOC` `#ThreatDetection` `#SIEM` `#CEF` `#Python` `#Flask`
