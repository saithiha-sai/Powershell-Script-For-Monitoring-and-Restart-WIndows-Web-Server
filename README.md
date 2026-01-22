# URL Monitor & IIS Auto-Restart Script – README

## Overview

`UrlMonitor-Restart404.ps1` is a **PowerShell monitoring script** designed to continuously check a web URL and **automatically restart IIS (W3SVC service)** when the website becomes unavailable or returns an error response.

All monitoring activities and recovery actions are logged to a file for audit and troubleshooting purposes.

This script is suitable for **production web servers**, **core banking web services**, and **critical internal applications** where high availability is required.

---

## Key Features

* Periodic URL health monitoring
* Detects HTTP failure or unreachable service
* Automatically restarts IIS (W3SVC)
* Continuous monitoring loop
* Detailed logging with timestamps
* Lightweight and agentless (PowerShell only)

---

## Prerequisites

* Windows Server with IIS installed
* PowerShell 5.1 or later
* Script must be run with **Administrator privileges**
* Network access to the monitored URL

---

## Script Parameters

```powershell
param(
    [string]$Url = 'http://192.168.xxxx',
    [int]$IntervalSeconds = 30,
    [string]$LogFile = "C:\Users\Administrator\Desktop\webchecklog.txt"
)
```

### Parameter Description

| Parameter       | Description                            |
| --------------- | -------------------------------------- |
| Url             | Web URL to monitor                     |
| IntervalSeconds | Time interval between checks (seconds) |
| LogFile         | File path for logging events           |

---

## How It Works

### 1. Logging Initialization

* Ensures the log file exists
* Logs script start time and configuration

### 2. URL Health Check

* Uses `Invoke-WebRequest` with HTTP **HEAD** method
* Timeout set to 10 seconds
* Captures HTTP status code

### 3. Status Evaluation

| Condition                  | Action                       |
| -------------------------- | ---------------------------- |
| HTTP 200                   | Service is UP (normal state) |
| HTTP failure / unreachable | Restart IIS                  |
| Other HTTP codes           | Logged as unexpected         |

---

## IIS Recovery Logic

When a failure is detected:

* IIS service (`W3SVC`) is restarted forcefully
* Success or failure is logged
* Script continues monitoring after restart

```powershell
Restart-Service -Name W3SVC -Force
```

---

## Logging Format

Each log entry contains:

* Timestamp
* Log level (INFO / ERROR)
* Action or event message

Example:

```text
2026-01-21 14:30:01 [INFO] HTTP 404 detected. Restarting IIS service...
2026-01-21 14:30:05 [INFO] IIS service restarted successfully
```

---

## Use Cases

* IIS-based application monitoring
* Core banking web interface availability
* Internal portal health checks
* Automated self-healing web services
* NOC / operations monitoring

---

## Running the Script

```powershell
powershell -ExecutionPolicy Bypass -File UrlMonitor-Restart404.ps1
```

(Optional) Run as a **Scheduled Task** for background execution.

---

## Notes & Limitations

* Script runs continuously (infinite loop)
* Requires stable DNS/network access
* Script restarts IIS only (not app pools)
* HEAD requests must be supported by the web server

---

## Future Enhancements

* Email or Telegram alert integration
* Application pool restart support
* Multiple URL monitoring
* Service recovery escalation logic
* Integration with Windows Event Log

---

## Author

**Sai Thiha**
IT System & Automation Engineer

---

✅ This script improves web service availability by providing automatic recovery and clear operational visibility.
