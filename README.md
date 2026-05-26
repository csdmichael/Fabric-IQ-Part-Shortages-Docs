# Fabric IQ — Parts Shortages Intelligence

An AI-powered shortage intelligence solution built on Microsoft Fabric, Fabric IQ Ontology, and Azure AI Foundry agents. It predicts, prioritizes, and recommends actions for parts shortages across a complex multi-plant supply chain.

---

## Table of Contents

1. [Business Problem](#1-business-problem)
2. [Solution Architecture](#2-solution-architecture)
3. [Fabric IQ Ontology](#3-fabric-iq-ontology)
4. [Cost / Benefit Analysis](#4-cost--benefit-analysis)
5. [User Interface](#5-user-interface)
   - [5.1 Business Operations](#51-business-operations)
   - [5.2 Admin / IT Operations](#52-admin--it-operations)
   - [5.3 System Design & Documentation](#53-system-design--documentation)
6. [Source Code Repository](#6-source-code-repository)

---

## 1. Business Problem

Capital‑equipment manufacturers face a recurring "shortage wave" in the 8 weeks leading up to each tool launch. A typical cycle starts with **~90,000 open shortages** at week 8 and must be driven down to a handful of urgent past‑due items by week 1. Today this is done with daily HTL stand‑ups, manual MAST triage, parallel reviews across Factory Support, Network Planning, SBM, and Order Fulfillment, and a heavy reliance on expedite freight and consignment draw‑downs. The process is labor‑intensive, duplicative across functions, and reactive — shortages reappear late in the cycle from supplier pull‑backs and demand swings.

![Part Shortage Business Problem](docs/Business-Problem/Part-Shortage-Business-Problem.png)

🎥 **Walkthrough video:** [Business Problem Overview](https://1drv.ms/v/c/4673b287399127d4/IQCfxQCkWM6sRIao0V0GmvgcAZo30myNJH4qXYodI0OjS9U?e=YVzQHC)

---

## 2. Solution Architecture

The solution is built on **Microsoft Fabric** (OneLake, Lakehouses, Notebooks, Data Agents) for unified data and ML, **Fabric IQ Ontology** for a semantic graph of supply‑chain entities, and **Azure AI Foundry agents** for orchestration and conversational experiences. ML models for shortage risk, demand forecasting, and action recommendation are trained in Fabric and surfaced through a web UI and a suite of AI assistants.

![Architecture](docs/Architecture.png)

🎥 **Walkthrough video:** [Architecture Overview](https://1drv.ms/v/c/4673b287399127d4/IQA4wuZ1X8G4T5TVD6xOMXh3AXnXwaDwD0zKlSOM6zXS3lY?e=uIOJry)

---

## 3. Fabric IQ Ontology

The **Fabric IQ Ontology** provides a semantic layer over OneLake tables, modeling supply‑chain concepts such as *Material*, *Plant*, *Shortage Event*, *Machine Configuration*, and the relationships between them. This lets both humans and AI agents reason over the data using business concepts instead of raw tables, and enables the Fabric Data Agent to answer natural‑language questions grounded in governed data.

| Ontology — Entities | Ontology — Material/Plant | Ontology — Shortage Event |
|---|---|---|
| ![Machine Config Header](docs/Screenshots/Fabric%20IQ%20Ontology/MachineConfigHeader.png) | ![Material Plant](docs/Screenshots/Fabric%20IQ%20Ontology/MaterialPlant.png) | ![Shortage Event](docs/Screenshots/Fabric%20IQ%20Ontology/ShortageEvent.png) |

🎥 **Walkthrough video:** [Fabric IQ Ontology Overview](https://1drv.ms/v/c/4673b287399127d4/IQDdRsI2DMAAR7c93slkDaq3ATl-gir-r_6HTpdtNzdMHtY?e=u1tZsM)

---

## 4. Cost / Benefit Analysis

The MVP targets a **50% reduction in shortages at week 1**, yielding an estimated **~$20.3M / year** in savings across three independent levers. Sensitivity across a 10%–75% reduction range produces **$4.0M – $30.4M / year**.

| Lever | Annual savings at 50% goal |
|---|---:|
| Labor automation (HTL meetings, triage, parallel reviews) | **$11.6M** |
| Expedite & consignment premium avoided | **$7.1M** |
| Revenue timing (fewer launch slips) | **$1.6M** |
| **Total** | **$20.3M / yr** |

Baseline workload: **~540,000 shortage events / yr** across **6 launch cycles**, **~175,500 labor hours / yr** at a $110/hr fully‑loaded rate.

📄 Full model, assumptions, formulas, and sensitivity table: [docs/BUSINESS_VALUE.md](docs/BUSINESS_VALUE.md)

---

## 5. User Interface

The application is organized into three functional zones:

### 5.1 Business Operations

Tools used by supply‑chain planners, SBMs, and operations leaders to monitor, predict, and act on shortages.

**Dashboards and Reports** — executive dashboards, at‑risk parts, severity breakdowns, supplier health, predictions feed, and the feedback‑learning loop:

| | |
|---|---|
| ![Menu](docs/Screenshots/UI/1.%20Business%20Operations/Dashboards%20and%20Reports/01.%20Menu.png) | ![Executive Dashboard](docs/Screenshots/UI/1.%20Business%20Operations/Dashboards%20and%20Reports/02.%20Part%20Shortages%20-%20Executive%20Dashboard.png) |
| ![Top At Risk Parts](docs/Screenshots/UI/1.%20Business%20Operations/Dashboards%20and%20Reports/03.%20Top%20At%20Risk%20Parts.png) | ![Shortages by Severity](docs/Screenshots/UI/1.%20Business%20Operations/Dashboards%20and%20Reports/04.a.%20Shortages%20by%20Severity.png) |
| ![Severity List](docs/Screenshots/UI/1.%20Business%20Operations/Dashboards%20and%20Reports/04.b.%20Shortages%20by%20Severity%20-%20List.png) | ![Resolution Paths](docs/Screenshots/UI/1.%20Business%20Operations/Dashboards%20and%20Reports/05.%20Shortage%20Resolution%20different%20Path.png) |
| ![Shortages by Plant](docs/Screenshots/UI/1.%20Business%20Operations/Dashboards%20and%20Reports/06.%20Shortages%20by%20Plant.png) | ![Supplier Health OTD](docs/Screenshots/UI/1.%20Business%20Operations/Dashboards%20and%20Reports/07.a.%20Supplier%20Health%20OTD.png) |
| ![Supplier Health Detail](docs/Screenshots/UI/1.%20Business%20Operations/Dashboards%20and%20Reports/07.b.%20Supplier%20Health%20-%20Detail.png) | ![Operational Report](docs/Screenshots/UI/1.%20Business%20Operations/Dashboards%20and%20Reports/08.%20Operational%20Report.png) |
| ![Executive Dashboard 2](docs/Screenshots/UI/1.%20Business%20Operations/Dashboards%20and%20Reports/09.%20Executive%20Dashboard.png) | ![Predictions & Recommendations](docs/Screenshots/UI/1.%20Business%20Operations/Dashboards%20and%20Reports/10.a.%20Predictions%20and%20recommendations.png) |
| ![Predictions Feed](docs/Screenshots/UI/1.%20Business%20Operations/Dashboards%20and%20Reports/10.b.%20Predictions%20Feed.png) | ![Feedback Learning Cycle](docs/Screenshots/UI/1.%20Business%20Operations/Dashboards%20and%20Reports/11.%20Feedback%20Learning%20Cycle.png) |

**AI Assistants** — conversational agents (Operations Data Assistant, Recommendation Copilot, Operations Orchestration Assistant) plus an Agent Prompt Lab:

| | |
|---|---|
| ![Ops Data Assistant - Try](docs/Screenshots/UI/1.%20Business%20Operations/AI%20Assistants/01.a.%20Operations%20Data%20Assistant%20-%20Try.png) | ![Ops Data Assistant - Trace](docs/Screenshots/UI/1.%20Business%20Operations/AI%20Assistants/01.b.%20Operations%20Data%20Assistant%20-%20Trace.png) |
| ![Ops Data Assistant - Feedback](docs/Screenshots/UI/1.%20Business%20Operations/AI%20Assistants/01.c.%20Operations%20Data%20Assistant%20-%20Feedback.png) | ![Recommendation Copilot](docs/Screenshots/UI/1.%20Business%20Operations/AI%20Assistants/02.%20Recommendation%20Copilot%20Assistant.png) |
| ![Operations Orchestration](docs/Screenshots/UI/1.%20Business%20Operations/AI%20Assistants/03.%20Operations%20Orchestration%20Assistant.png) | ![Agent Prompt Lab - Samples](docs/Screenshots/UI/1.%20Business%20Operations/AI%20Assistants/04.a.%20Agent%20Prompt%20Lab%20-%20Samples.png) |
| ![Agent Prompt Lab - Try in Chat](docs/Screenshots/UI/1.%20Business%20Operations/AI%20Assistants/04.b.%20Agent%20Prompt%20Lab%20-%20Try%20in%20Chat.png) | |

### 5.2 Admin / IT Operations

Tools used by IT and ML engineers to manage data ingestion, feature engineering, model training, and model performance monitoring.

| | |
|---|---|
| ![Menu](docs/Screenshots/UI/2.%20Admin%20IT%20Operations/01.%20Menu.png) | ![Data Ingestion](docs/Screenshots/UI/2.%20Admin%20IT%20Operations/02.%20Data%20Ingestion.png) |
| ![Feature Engineering](docs/Screenshots/UI/2.%20Admin%20IT%20Operations/03.a.%20Feature%20Engineering.png) | ![Feature Engineering - Selected](docs/Screenshots/UI/2.%20Admin%20IT%20Operations/03.b.%20Feature%20Engineering%20-%20Selected%20Features.png) |
| ![Model Training](docs/Screenshots/UI/2.%20Admin%20IT%20Operations/04.%20Model%20Training.png) | ![Perf - Action Recommender](docs/Screenshots/UI/2.%20Admin%20IT%20Operations/05.a.%20ML%20Model%20Performance%20-%20Action%20Recommender.png) |
| ![Perf - Demand Forecaster](docs/Screenshots/UI/2.%20Admin%20IT%20Operations/05.b.%20ML%20Model%20Performance%20-%20Demand%20Forecaster.png) | ![Perf - Shortage Risk Calculator](docs/Screenshots/UI/2.%20Admin%20IT%20Operations/05.c.%20ML%20Model%20Performance%20-%20Shortage%20Risk%20Calculator.png) |
| ![Predictions and Insights](docs/Screenshots/UI/2.%20Admin%20IT%20Operations/06.%20Predictions%20and%20Insights.png) | |

### 5.3 System Design & Documentation

In‑app architecture, dataflow, ML algorithm, table catalog, and ontology‑explorer views for solution discoverability.

| | |
|---|---|
| ![Menu](docs/Screenshots/UI/3.%20System%20Design%20and%20Documentation/01.%20Menu.png) | ![Architecture](docs/Screenshots/UI/3.%20System%20Design%20and%20Documentation/02.%20Architecture.png) |
| ![Solution Dataflow](docs/Screenshots/UI/3.%20System%20Design%20and%20Documentation/03.%20Solution%20Dataflow.png) | ![ML Algorithms](docs/Screenshots/UI/3.%20System%20Design%20and%20Documentation/04.%20ML%20Algos.png) |
| ![App URLs](docs/Screenshots/UI/3.%20System%20Design%20and%20Documentation/05.%20App%20Urls.png) | ![OneLake Tables](docs/Screenshots/UI/3.%20System%20Design%20and%20Documentation/06.%20OneLake%20Tables.png) |
| ![ML Output Tables](docs/Screenshots/UI/3.%20System%20Design%20and%20Documentation/07.%20ML%20Output%20Tables.png) | ![Drill-through Shortages](docs/Screenshots/UI/3.%20System%20Design%20and%20Documentation/08.%20Drill%20through%20OneLake%20Table%20-%20Shortages.png) |
| ![Ontology Explorer](docs/Screenshots/UI/3.%20System%20Design%20and%20Documentation/09.a.%20Fabric%20Ontology%20Explorer.png) | ![Ontology - Relationships](docs/Screenshots/UI/3.%20System%20Design%20and%20Documentation/09.b.%20Fabric%20Ontology%20Explorer%20-%20Relationships.png) |
| ![Ontology - Entities & Attrs](docs/Screenshots/UI/3.%20System%20Design%20and%20Documentation/09.c.%20Fabric%20Ontology%20Explorer%20-%20Entities%20and%20Attrs.png) | |

Single sign‑on / OTP login is supported:

![Login via SSO or OTP](docs/Screenshots/UI/Login%20via%20SSO%20or%20OTP.png)

---

## 6. Source Code Repository

The source code lives in a **private** GitHub repository:

🔒 **[github.com/csdmichael/Fabric-IQ-Ontology-Parts-Shortages](https://github.com/csdmichael/Fabric-IQ-Ontology-Parts-Shortages)**

For access, please contact the repository owner ([@csdmichael](https://github.com/csdmichael)) with your GitHub username and a brief description of your use case.
