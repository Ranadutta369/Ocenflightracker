<div align="center">

# 🚢 OceanFlightTracker

### *Automated Multimodal Freight Intelligence, Email Data Parsing, and Carrier Rate Engine*

[![Production Status](https://img.shields.io/badge/Production-Live-00C853?style=for-the-badge&logo=render&logoColor=white)](https://ocenflightracker.onrender.com/)
[![Python Version](https://img.shields.io/badge/Python-3.10%2B-3776AB?style=for-the-badge&logo=python&logoColor=white)](https://www.python.org/)
[![Deployment](https://img.shields.io/badge/Hosted_On-Render-46E3B7?style=for-the-badge&logo=render&logoColor=black)](https://ocenflightracker.onrender.com/)
[![License](https://img.shields.io/badge/License-MIT-blue?style=for-the-badge)](LICENSE)

[**Explore Live Platform »**](https://ocenflightracker.onrender.com/) • [**Report Bug**](https://github.com/Ranadutta369/Ocenflightracker/issues) • [**Request Feature**](https://github.com/Ranadutta369/Ocenflightracker/issues)

---

</div>

## 📌 Executive Overview

**OceanFlightTracker** is an automated web platform and microservice built to solve the operational friction of manual freight tracking, fragmented ocean/air rate matrices, and unstructured logistics email logs.

Freight forwarding operations often suffer from operational bottlenecks: tracking data is locked inside unformatted carrier emails, rate comparisons require manual spreadsheet lookups, and invoice reconciliation is error-prone. OceanFlightTracker integrates these workflows into a unified platform:

* 📧 **Regex & Heuristic Email Parsing:** Automatically ingests unstructured logistics communications (`sample_emails.txt`), extracting Air Waybills (AWB), container IDs, origin/destination ports, and milestone dates.
* 📊 **Dynamic Freight Cost Calculations:** Queries structured rate datasets (`carrier_rates.csv`), applying volumetric, fuel, and origin-destination surcharge matrices in real time.
* ⚡ **Production-Hardened Microservice Architecture:** Powered by a Python WSGI backend (`main.py`) running on Gunicorn, with IaC automated deployment via `render.yaml`.

---

## 🏗️ System Architecture & Workflow

```
                        ┌─────────────────────────────────────────┐
                        │      UNSTRUCTURED DATA SOURCES          │
                        │  - sample_emails.txt (Tracking Logs)    │
                        │  - carrier_rates.csv (Pricing Matrix)   │
                        └────────────────────┬────────────────────┘
                                             │
                                             ▼
                        ┌─────────────────────────────────────────┐
                        │     APPLICATION CORE (main.py)          │
                        │  - Dynamic DATA_DIR Path Resolver       │
                        │  - Regex Metadata & Identifier Parser   │
                        │  - Tabular Surcharge & Cost Engine      │
                        └────────────────────┬────────────────────┘
                                             │
                                             ▼
                        ┌─────────────────────────────────────────┐
                        │     PRESENTATION & DEPLOYMENT LAYER     │
                        │  - Dynamic Jinja2 Views (templates/)    │
                        │  - Async DOM Scripting (static/js/)     │
                        │  - Production WSGI Worker (Procfile)    │
                        │  - Live Hosted App (Render Cloud)       │
                        └─────────────────────────────────────────┘
```

---

## ✨ Key Platform Features

| Module | Technical Implementation | Operational Impact |
| :--- | :--- | :--- |
| **Email Text Mining** | Regular expression parsing engine executed in `main.py`. | Converts unstructured notification emails into normalized JSON data. |
| **Carrier Rate Engine** | Tabular lookup algorithms matching corridors against `carrier_rates.csv`. | Delivers instant freight quote calculations and carrier comparisons. |
| **Interactive Dashboard** | Server-rendered Jinja2 templates (`templates/`) combined with asynchronous client-side JavaScript (`static/js/`). | Eliminates manual tracking steps with a single web interface. |
| **Environment Isolation** | Environment variable mapping (`DATA_DIR`) for file access inside cloud containers. | Prevents `FileNotFoundError` exceptions during ephemeral container scaling. |
| **DevOps Pipeline** | Declarative IaC specification in `render.yaml` with Gunicorn worker setup in `Procfile`. | Supports continuous deployment straight from GitHub pushes. |

---

## 📐 Mathematical Formulation: Rate & Volumetric Model

Freight pricing calculations inside `main.py` follow standardized dimensional weight formulas before applying surcharge schedules:

$$\text{Volumetric Weight (kg)} = \frac{\text{Length (cm)} \times \text{Width (cm)} \times \text{Height (cm)}}{6000}$$

$$\text{Chargeable Weight} = \max(\text{Gross Weight}, \text{Volumetric Weight})$$

$$\text{Total Freight Cost} = (\text{Chargeable Weight} \times \text{Base Rate}_{\text{Carrier}}) + \text{Surcharge}_{\text{Fuel}} + \text{Surcharge}_{\text{Terminal}}$$

---

## 📂 Repository Topology

```text
.
├── main.py              # WSGI server controller, regex email parser, and rate engine
├── carrier_rates.csv    # Structured database of carrier corridors, rates, and transit times
├── sample_emails.txt    # Unstructured logistics communications and tracking email dataset
├── render.yaml          # Declarative Infrastructure-as-Code (IaC) configuration for Render
├── Procfile            # Gunicorn process execution setup for production deployment
├── requirements.txt    # Pinned dependency definitions
├── runtime.txt         # Python runtime environment declaration
├── .gitignore          # Version control filters blocking build artifacts and temporary files
├── templates/          # Jinja2 server-rendered web interface templates
└── static/
    └── js/             # Asynchronous client-side JavaScript UI handlers
```

---

## 🚀 Local Development Setup

### Prerequisites

* **Python 3.10+** installed on your local system.
* **Git** version control system installed.

### Step-by-Step Installation

1. **Clone the repository:**
   ```bash
   git clone https://github.com/Ranadutta369/Ocenflightracker.git
   cd Ocenflightracker
   ```

2. **Create and activate a virtual environment:**
   ```bash
   # On macOS/Linux
   python3 -m venv venv
   source venv/bin/activate

   # On Windows
   python -m venv venv
   venv\Scripts\activate
   ```

3. **Install pinned dependencies:**
   ```bash
   pip install -r requirements.txt
   ```

4. **Configure environment variables:**
   Set the local data directory path (defaults to the workspace root if omitted):
   ```bash
   # On macOS/Linux
   export DATA_DIR="$(pwd)"

   # On Windows (PowerShell)
   $env:DATA_DIR=(Get-Location).Path
   ```

5. **Launch the local development server:**
   ```bash
   python main.py
   ```
   Navigate to `http://127.0.0.1:5000` in your web browser.

---

## ☁️ Production Deployment (Render)

This repository is built for continuous integration and automated deployment on **Render**.

### Deployment Files
* **`render.yaml`**: Configures the web service environment, runtime dependencies, build commands, and instance types.
* **`Procfile`**: Directs the platform to start Gunicorn web workers:
  ```text
  web: gunicorn main:app
  ```

> [!NOTE]
> The application uses an environment variable named `DATA_DIR` inside `main.py`. This ensures file paths referencing `carrier_rates.csv` and `sample_emails.txt` resolve correctly in containerized production environments.

---

## 🛣️ Development Roadmap

> [!TIP]
> Planned features and technical enhancements for upcoming releases:

- [ ] **Database Migration:** Upgrade flat files (`carrier_rates.csv`) to PostgreSQL with SQLAlchemy ORM.
- [ ] **Live Telemetry APIs:** Integrate MarineTraffic AIS and FlightAware ADS-B APIs for live GPS vessel and flight tracking.
- [ ] **Automated Webhooks:** Set up inbound Mailgun/SendGrid email webhooks to parse tracking emails directly as they arrive.
- [ ] **PDF Report Generation:** Add automated exporting for invoice audits and shipment status summaries.

---

## 🤝 Contributing

Contributions are welcome! Follow these steps to contribute:

1. **Fork** the Repository.
2. **Create** a Feature Branch (`git checkout -b feature/AmazingFeature`).
3. **Commit** your changes (`git commit -m 'Add AmazingFeature'`).
4. **Push** to the Branch (`git push origin feature/AmazingFeature`).
5. **Open** a Pull Request.

---

## 📄 License

Distributed under the **MIT License**. See `LICENSE` for more information.

---

<div align="center">

**Developed by [OceanFlightTracker Team](https://github.com/Ranadutta369/Ocenflightracker)**  
*Global Multimodal Freight Intelligence & Tracking Infrastructure*

</div>
