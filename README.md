# ScaleShield

**Metrics-Driven Auto-Scaling & Self-Healing Platform**

One-week DevOps technical project.

## About the project

Applications that run a fixed number of instances cannot follow changing demand. They slow down under heavy load, waste resources when traffic is low, and stay broken until someone notices a crashed container.

ScaleShield addresses this with two DevOps practices:

- **Auto-scaling:** add or remove application replicas based on CPU utilization and request load, within a configured minimum and maximum.
- **Self-healing:** probe every replica's health endpoint, restart replicas that fail, and confirm they recover before they receive traffic again.

Decisions come from a deterministic rule engine. A scaling action happens only after a threshold is breached for several consecutive samples and the cooldown period has passed, which prevents replicas from flapping up and down on short spikes.

## What is in this repository

This repository contains the **web dashboard demo** and its screenshots.

| Path | Description |
|---|---|
| `index.html` | ScaleShield dashboard. Single file, no build step. Runs on sample data. |
| `docs/screenshots/` | Screenshots of every dashboard page. |

The dashboard is a front-end demonstration. Its charts and tables use fixed sample data, and the buttons do not control real containers. The full platform design (FastAPI backend, Docker SDK, Nginx, PostgreSQL) is not implemented in this repository.

## Dashboard pages

| Page | Shows |
|---|---|
| Overview | Replica count, average CPU, request rate, replica status |
| Replicas | Replica list with health, CPU, memory, latency, uptime; load balancer pool |
| Live Monitoring | CPU, memory, request rate, average and p95 latency charts |
| Scaling Policies | Scale-out and scale-in thresholds, replica limits, cooldown, health-check settings |
| Scaling Events | History of scale-out, scale-in, and no-action decisions |
| Self-Healing Incidents | Recovery cycle and log of detected failures |
| Audit Logs | Traceable record of system and operator actions |
| Settings & Load Test | Load and failure simulation controls, system environment |

## Screenshots

| | |
|---|---|
| ![Overview](docs/screenshots/01-overview.png) | ![Replicas](docs/screenshots/02-replicas.png) |
| ![Live Monitoring](docs/screenshots/03-monitoring.png) | ![Scaling Policies](docs/screenshots/04-policies.png) |
| ![Scaling Events](docs/screenshots/05-events.png) | ![Self-Healing Incidents](docs/screenshots/06-incidents.png) |
| ![Audit Logs](docs/screenshots/07-audit.png) | ![Settings](docs/screenshots/08-settings.png) |

## Run locally

No installation is needed. Either open `index.html` in a browser, or serve the folder:

```bash
git clone https://github.com/asish358/devops_project.git
cd devops_project
python3 -m http.server 8000
```

Then open <http://localhost:8000>. Pages are reached through the sidebar (for example `#replicas`, `#monitoring`).

## Deploy with GitHub Pages

1. Open the repository on GitHub, then **Settings**, then **Pages**.
2. Under **Build and deployment**, set **Source** to **Deploy from a branch**.
3. Choose branch **main** and folder **/ (root)**, then **Save**.
4. After a minute the site is live at `https://asish358.github.io/devops_project/`.

## Proposed architecture

```mermaid
flowchart LR
    U[Users / Load generator] --> LB[Nginx load balancer]
    LB --> R1[App replica 1]
    LB --> R2[App replica 2]
    LB --> RN[App replica N]
    MC[Metrics collector] -->|CPU, memory, latency, health| R1
    MC --> R2
    MC --> RN
    MC --> DE[Scaling decision engine]
    DE --> CC[Container controller<br/>Docker SDK]
    CC -->|add / remove / restart| R1
    HM[Health monitor<br/>self-healing] --> CC
    DE --> DB[(SQLite / PostgreSQL)]
    HM --> DB
    API[FastAPI backend] --> DB
    FE[React dashboard] --> API
```

## Scaling rules used in the demo

| Setting | Value |
|---|---|
| Scale-out threshold | CPU above 70% |
| Scale-in threshold | CPU below 30% |
| Consecutive samples required | 3 |
| Minimum / maximum replicas | 2 / 6 |
| Cooldown | 60 seconds |
| Health-check interval | 5 seconds |
| Failed probes before restart | 3 |

## Technologies

- **Dashboard (this repository):** HTML, CSS, and JavaScript with inline SVG charts.
- **Full platform (proposed design):** Python, FastAPI, SQLAlchemy, React, TypeScript, Docker, Docker Compose, Nginx, PostgreSQL, Pytest, Vitest, GitHub Actions.

## Limitations

- The dashboard uses sample data and does not control real containers.
- No authentication or role-based access control.
- Single Docker host only in the proposed design; no Kubernetes support.
- External alerting (Slack, PagerDuty) is not included.

## Future scope

- Real FastAPI backend and Docker SDK container control
- Authentication and role-based access control
- Kubernetes and multi-node support
- Persistent policies and real infrastructure metrics
- Slack and PagerDuty alerts
- Predictive scaling from historical metrics
