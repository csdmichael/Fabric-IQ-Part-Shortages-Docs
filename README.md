# Fabric IQ — Parts Shortages Intelligence

An AI-powered shortage intelligence solution built on Microsoft Fabric, Fabric IQ Ontology, and Azure AI Foundry agents. It predicts, prioritizes, and recommends actions for parts shortages across a complex multi-plant supply chain.

---

## Table of Contents

1. [Business Problem](#1-business-problem)
2. [Solution Architecture](#2-solution-architecture)
3. [Fabric IQ Ontology](#3-fabric-iq-ontology)
4. [ML Algorithms](#4-ml-algorithms)
5. [Cost / Benefit Analysis](#5-cost--benefit-analysis)
6. [User Interface](#6-user-interface)
   - [6.1 Business Operations](#61-business-operations)
   - [6.2 Admin / IT Operations](#62-admin--it-operations)
   - [6.3 System Design & Documentation](#63-system-design--documentation)
7. [Deployment Guide](#7-deployment-guide)
8. [Source Code Repository](#8-source-code-repository)
9. [Future Ideas — Extending the POC](#9-future-ideas--extending-the-poc)

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

**Ontology Overview** — End‑to‑end map of the Parts Shortages ontology: entities (master, bridge, and operational/event), their relationships, and the derived properties computed by the ontology that power downstream reasoning (severity bands, risk flags, cover‑days, supplier scores, PO lateness).

![Parts Shortages Ontology Overview](docs/Ontology-Overview.png)

**Machine Configuration Header** — The top‑level *MachineConfigHeader* entity that represents a tool/system configuration. It is the anchor object linking bills of materials, plants, and downstream shortage events back to a specific machine build.

![Machine Config Header](docs/Screenshots/Fabric%20IQ%20Ontology/MachineConfigHeader.png)

**Material / Plant relationship** — The *Material* and *Plant* entities and their many‑to‑many association. This view shows how a given part number is sourced, stocked, and consumed across multiple manufacturing plants, which is the basis for cross‑plant reallocation recommendations.

![Material Plant](docs/Screenshots/Fabric%20IQ%20Ontology/MaterialPlant.png)

**Shortage Event entity** — The *ShortageEvent* entity with its attributes (severity, past‑due flag, root‑cause code, recommended action) and its relationships to Material, Plant, Supplier, and Machine Configuration. This is the core fact the AI agents and dashboards reason over.

![Shortage Event](docs/Screenshots/Fabric%20IQ%20Ontology/ShortageEvent.png)

🎥 **Walkthrough video:** [Fabric IQ Ontology Overview](https://1drv.ms/v/c/4673b287399127d4/IQDdRsI2DMAAR7c93slkDaq3ATl-gir-r_6HTpdtNzdMHtY?e=u1tZsM)

---

## 4. ML Algorithms

Three purpose-built ML models — all trained and served from **Microsoft Fabric** on features derived from the Fabric IQ Ontology — power the predictive and prescriptive layer of the solution:

- **Shortage Risk Calculator** — A gradient-boosted classifier that scores every open shortage with the probability of slipping past its required date. Inputs include supplier OTD history, lead-time variance, MRP signal stability, plant load, and machine-configuration criticality. Output is a calibrated risk score used to rank the *Top At-Risk Parts* view and drive proactive triage.
- **Demand Forecaster** — A time-series model (gradient-boosted regression over engineered lag/seasonality features) that projects net part demand per material/plant across the 8-week launch cycle. Outputs feed the shortage-prediction pipeline and the cross-plant reallocation logic, and are back-tested with MAPE / RMSE / bias trend.
- **Action Recommender** — A multi-class classifier that, given a scored shortage and the current supply state, recommends the best resolution path (expedite, reallocate from sister plant, substitute, re-plan, escalate). Trained on historical resolutions and continuously refined via the planner accept/reject feedback loop.

All three models are retrained on a scheduled cadence from curated gold-layer tables in OneLake, their outputs are written back as ML output tables, and their live performance (accuracy, precision/recall, MAPE, acceptance rate) is monitored in the Admin / IT Operations dashboards.

🎥 **Walkthrough video:** [ML Algorithms Overview](https://1drv.ms/v/c/4673b287399127d4/IQAqnV510hZFSIfadk20E9nUAcarb-BvMMudFbONGwM5_FM?e=DdzESB)

---

## 5. Cost / Benefit Analysis

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

## 6. User Interface

**UI URL:** https://fabriciq-shortages-ui-b3.azurewebsites.net

The application is organized into three functional zones:

### 6.1 Business Operations

Tools used by supply‑chain planners, SBMs, and operations leaders to monitor, predict, and act on shortages.

#### Dashboards and Reports

🎥 **Walkthrough video:** [Business Operations — Reports](https://1drv.ms/v/c/4673b287399127d4/IQC9c-Sd95JwQ5aGxGO59RNuAXNRLlVvdjEi8k2PGcSv5GY?e=5C9hds)

**Navigation menu** — Entry point for all operations dashboards, reports, and drill‑through views.

![Menu](docs/Screenshots/UI/1.%20Business%20Operations/Dashboards%20and%20Reports/01.%20Menu.png)

**Part Shortages — Executive Dashboard** — Cycle‑level KPIs (open shortages, past‑due count, week‑over‑week trend) tailored for supply‑chain leadership.

![Executive Dashboard](docs/Screenshots/UI/1.%20Business%20Operations/Dashboards%20and%20Reports/02.%20Part%20Shortages%20-%20Executive%20Dashboard.png)

**Top At‑Risk Parts** — Ranked list of parts most likely to slip, scored by the Shortage Risk Calculator ML model and enriched with supplier and plant context.

![Top At Risk Parts](docs/Screenshots/UI/1.%20Business%20Operations/Dashboards%20and%20Reports/03.%20Top%20At%20Risk%20Parts.png)

**Shortages by Severity** — Visual breakdown of open shortages by severity tier (critical / high / medium / low) for triage prioritization.

![Shortages by Severity](docs/Screenshots/UI/1.%20Business%20Operations/Dashboards%20and%20Reports/04.a.%20Shortages%20by%20Severity.png)

**Shortages by Severity — Detail list** — Row‑level drill‑down behind the severity tile, showing each shortage with material, plant, due date, and recommended action.

![Severity List](docs/Screenshots/UI/1.%20Business%20Operations/Dashboards%20and%20Reports/04.b.%20Shortages%20by%20Severity%20-%20List.png)

**Shortage Resolution — Paths** — Sankey/funnel view of how shortages move through the resolution paths (expedite, reallocate, substitute, re‑plan) with throughput at each step.

![Resolution Paths](docs/Screenshots/UI/1.%20Business%20Operations/Dashboards%20and%20Reports/05.%20Shortage%20Resolution%20different%20Path.png)

**Shortages by Plant** — Heat‑map / bar view of open shortages distributed across manufacturing plants to spot regional hotspots.

![Shortages by Plant](docs/Screenshots/UI/1.%20Business%20Operations/Dashboards%20and%20Reports/06.%20Shortages%20by%20Plant.png)

**Supplier Health — On‑Time Delivery** — Supplier scorecard ranking vendors by OTD%, lead‑time variance, and shortage contribution.

![Supplier Health OTD](docs/Screenshots/UI/1.%20Business%20Operations/Dashboards%20and%20Reports/07.a.%20Supplier%20Health%20OTD.png)

**Supplier Health — Detail** — Drill‑through into an individual supplier with PO history, defect rate, and the shortages they are currently driving.

![Supplier Health Detail](docs/Screenshots/UI/1.%20Business%20Operations/Dashboards%20and%20Reports/07.b.%20Supplier%20Health%20-%20Detail.png)

**Operational Report** — Tabular operational report consumed in HTL stand‑ups, exportable for offline review.

![Operational Report](docs/Screenshots/UI/1.%20Business%20Operations/Dashboards%20and%20Reports/08.%20Operational%20Report.png)

**Executive Dashboard (rollup)** — Cross‑program executive rollup combining shortage trend, savings realized, and AI‑agent activity in a single board view.

![Executive Dashboard 2](docs/Screenshots/UI/1.%20Business%20Operations/Dashboards%20and%20Reports/09.%20Executive%20Dashboard.png)

**Predictions and Recommendations** — ML‑generated forecasts paired with the Action Recommender's top suggested resolution per shortage.

![Predictions & Recommendations](docs/Screenshots/UI/1.%20Business%20Operations/Dashboards%20and%20Reports/10.a.%20Predictions%20and%20recommendations.png)

**Predictions Feed** — Streaming feed of newly generated predictions as fresh data lands in OneLake, with confidence scores.

![Predictions Feed](docs/Screenshots/UI/1.%20Business%20Operations/Dashboards%20and%20Reports/10.b.%20Predictions%20Feed.png)

**Feedback / Learning Cycle** — Closed‑loop view where planner accept/reject decisions on recommendations are captured and fed back into model retraining.

![Feedback Learning Cycle](docs/Screenshots/UI/1.%20Business%20Operations/Dashboards%20and%20Reports/11.%20Feedback%20Learning%20Cycle.png)

#### AI Assistants

🎥 **Walkthrough video:** [Business Operations — AI Assistants](https://1drv.ms/v/c/4673b287399127d4/IQBvYxrYreY8QrXqWod_Il1TARzApnoJakz_fat9K4zvSm4?e=O1d4Gj)

**Operations Data Assistant — Try** — Natural‑language Q&A over the OneLake ontology (powered by Fabric Data Agent). Planners can ask things like "show me all critical shortages at Plant 4 due this week."

![Ops Data Assistant - Try](docs/Screenshots/UI/1.%20Business%20Operations/AI%20Assistants/01.a.%20Operations%20Data%20Assistant%20-%20Try.png)

**Operations Data Assistant — Trace** — Inspector view showing the agent's reasoning trace: tool calls, generated SQL/KQL, retrieved rows, and the final grounded answer.

![Ops Data Assistant - Trace](docs/Screenshots/UI/1.%20Business%20Operations/AI%20Assistants/01.b.%20Operations%20Data%20Assistant%20-%20Trace.png)

**Operations Data Assistant — Feedback** — Thumbs‑up/down plus comment capture on each answer, feeding the continuous‑evaluation pipeline.

![Ops Data Assistant - Feedback](docs/Screenshots/UI/1.%20Business%20Operations/AI%20Assistants/01.c.%20Operations%20Data%20Assistant%20-%20Feedback.png)

**Recommendation Copilot Assistant** — Conversational front‑end to the Action Recommender model — explains *why* a given action is recommended and lets the user accept, modify, or reject.

![Recommendation Copilot](docs/Screenshots/UI/1.%20Business%20Operations/AI%20Assistants/02.%20Recommendation%20Copilot%20Assistant.png)

**Operations Orchestration Assistant** — The Foundry orchestrator agent that routes user intents across the Data Agent, Recommendation Copilot, and downstream workflow tools.

![Operations Orchestration](docs/Screenshots/UI/1.%20Business%20Operations/AI%20Assistants/03.%20Operations%20Orchestration%20Assistant.png)

**Agent Prompt Lab — Samples** — Library of curated prompt samples for each assistant, used for onboarding and regression testing.

![Agent Prompt Lab - Samples](docs/Screenshots/UI/1.%20Business%20Operations/AI%20Assistants/04.a.%20Agent%20Prompt%20Lab%20-%20Samples.png)

**Agent Prompt Lab — Try in Chat** — Interactive sandbox to run sample prompts in chat against any selected agent and inspect the response.

![Agent Prompt Lab - Try in Chat](docs/Screenshots/UI/1.%20Business%20Operations/AI%20Assistants/04.b.%20Agent%20Prompt%20Lab%20-%20Try%20in%20Chat.png)

### 6.2 Admin / IT Operations

Tools used by IT and ML engineers to manage data ingestion, feature engineering, model training, and model performance monitoring.

🎥 **Walkthrough video:** [Admin / IT Operations Overview](https://1drv.ms/v/c/4673b287399127d4/IQBZ6S17GP8HQ5vW3PWkLwFXAQuwbKL1GoCAPt6NtoCIk3g?e=iDK3NZ)

**Admin menu** — Entry point for the data, feature, training, and model‑monitoring tools.

![Menu](docs/Screenshots/UI/2.%20Admin%20IT%20Operations/01.%20Menu.png)

**Data Ingestion** — Status board for ingestion pipelines bringing source data (ECC shortages, MRP, supplier feeds) into OneLake Lakehouses.

![Data Ingestion](docs/Screenshots/UI/2.%20Admin%20IT%20Operations/02.%20Data%20Ingestion.png)

**Feature Engineering — Catalog** — Browse the full feature catalog (lead‑time variance, supplier OTD, demand volatility, etc.) generated from the ontology.

![Feature Engineering](docs/Screenshots/UI/2.%20Admin%20IT%20Operations/03.a.%20Feature%20Engineering.png)

**Feature Engineering — Selected Features** — Per‑model selected feature set with importance scores, used as inputs for the training pipeline.

![Feature Engineering - Selected](docs/Screenshots/UI/2.%20Admin%20IT%20Operations/03.b.%20Feature%20Engineering%20-%20Selected%20Features.png)

**Model Training** — Training run dashboard: kick off retraining, monitor progress, and compare hyperparameter sweeps.

![Model Training](docs/Screenshots/UI/2.%20Admin%20IT%20Operations/04.%20Model%20Training.png)

**ML Model Performance — Action Recommender** — Accuracy, precision/recall, and acceptance‑rate metrics for the Action Recommender model.

![Perf - Action Recommender](docs/Screenshots/UI/2.%20Admin%20IT%20Operations/05.a.%20ML%20Model%20Performance%20-%20Action%20Recommender.png)

**ML Model Performance — Demand Forecaster** — Forecast error (MAPE, RMSE), bias trend, and back‑testing for the Demand Forecaster.

![Perf - Demand Forecaster](docs/Screenshots/UI/2.%20Admin%20IT%20Operations/05.b.%20ML%20Model%20Performance%20-%20Demand%20Forecaster.png)

**ML Model Performance — Shortage Risk Calculator** — ROC/AUC, calibration, and confusion matrix for the Shortage Risk Calculator classifier.

![Perf - Shortage Risk Calculator](docs/Screenshots/UI/2.%20Admin%20IT%20Operations/05.c.%20ML%20Model%20Performance%20-%20Shortage%20Risk%20Calculator.png)

**Predictions and Insights** — Operational view of the latest model outputs being written back to OneLake for downstream dashboards and agents.

![Predictions and Insights](docs/Screenshots/UI/2.%20Admin%20IT%20Operations/06.%20Predictions%20and%20Insights.png)

### 6.3 System Design & Documentation

In‑app architecture, dataflow, ML algorithm, table catalog, and ontology‑explorer views for solution discoverability.

🎥 **Walkthrough video:** [System Design & Documentation Overview](https://1drv.ms/v/c/4673b287399127d4/IQD11R0OHtP0Rqt378p3wPFzATjISyvAAFFNcJzePjdhjhw?e=K6G1qC)

**Documentation menu** — Entry point for the in‑app system design and documentation views.

![Menu](docs/Screenshots/UI/3.%20System%20Design%20and%20Documentation/01.%20Menu.png)

**Architecture** — End‑to‑end architecture diagram rendered inside the app: source systems → Fabric ingestion → Lakehouse → Ontology → ML → Foundry agents → UI.

![Architecture](docs/Screenshots/UI/3.%20System%20Design%20and%20Documentation/02.%20Architecture.png)

**Solution Dataflow** — Logical dataflow showing how a raw shortage record propagates through bronze/silver/gold layers and into predictions and recommendations.

![Solution Dataflow](docs/Screenshots/UI/3.%20System%20Design%20and%20Documentation/03.%20Solution%20Dataflow.png)

**ML Algorithms** — Reference card for each model (Shortage Risk Calculator, Demand Forecaster, Action Recommender): algorithm family, inputs, outputs, and refresh cadence.

![ML Algorithms](docs/Screenshots/UI/3.%20System%20Design%20and%20Documentation/04.%20ML%20Algos.png)

**App URLs** — Catalog of all deployed endpoints (web app, Foundry agents, Fabric workspaces) for quick navigation.

![App URLs](docs/Screenshots/UI/3.%20System%20Design%20and%20Documentation/05.%20App%20Urls.png)

**OneLake Tables** — Catalog of the source/curated tables in OneLake with descriptions and row counts.

![OneLake Tables](docs/Screenshots/UI/3.%20System%20Design%20and%20Documentation/06.%20OneLake%20Tables.png)

**ML Output Tables** — Catalog of model‑output tables (predictions, recommendations, scores) written back to OneLake for downstream consumption.

![ML Output Tables](docs/Screenshots/UI/3.%20System%20Design%20and%20Documentation/07.%20ML%20Output%20Tables.png)

**Drill‑through — Shortages table** — Browse the raw `ecc.zspm_shortages` rows directly from the documentation view for validation and lineage checks.

![Drill-through Shortages](docs/Screenshots/UI/3.%20System%20Design%20and%20Documentation/08.%20Drill%20through%20OneLake%20Table%20-%20Shortages.png)

**Fabric Ontology Explorer** — Interactive ontology browser embedded in the app, showing the full set of entities defined in Fabric IQ.

![Ontology Explorer](docs/Screenshots/UI/3.%20System%20Design%20and%20Documentation/09.a.%20Fabric%20Ontology%20Explorer.png)

**Ontology Explorer — Relationships** — Graph view of relationships (Material→Plant, Plant→Shortage, Supplier→Material, etc.) used by the Data Agent for grounded retrieval.

![Ontology - Relationships](docs/Screenshots/UI/3.%20System%20Design%20and%20Documentation/09.b.%20Fabric%20Ontology%20Explorer%20-%20Relationships.png)

**Ontology Explorer — Entities & Attributes** — Per‑entity attribute schema with data types and semantic descriptions; this is what makes natural‑language Q&A reliable.

![Ontology - Entities & Attrs](docs/Screenshots/UI/3.%20System%20Design%20and%20Documentation/09.c.%20Fabric%20Ontology%20Explorer%20-%20Entities%20and%20Attrs.png)

**Login — SSO / OTP** — The app supports Entra ID single sign‑on with a one‑time‑passcode fallback for external/guest users.

![Login via SSO or OTP](docs/Screenshots/UI/Login%20via%20SSO%20or%20OTP.png)

---

## 7. Deployment Guide

Fabric IQ deploys into a customer tenant via a short, table‑driven runbook: three manual Azure resources (Resource Group, Fabric capacity, Foundry account) plus a one‑time Entra app‑registration handoff, followed by five idempotent PowerShell scripts that provision, wire, and verify everything else from a single `azure.config.json` source of truth. End‑to‑end setup typically runs ~30 minutes when Subscription Owner, Entra admin, and Fabric admin roles are held by the same person.

📄 Full prerequisites, role matrix, manual steps, automated pipeline, and troubleshooting: [docs/DEPLOYMENT_GUIDE.md](docs/DEPLOYMENT_GUIDE.md)

---

## 8. Source Code Repository

The source code lives in a **private** GitHub repository:

🔒 **[github.com/csdmichael/Fabric-IQ-Ontology-Parts-Shortages](https://github.com/csdmichael/Fabric-IQ-Ontology-Parts-Shortages)**

For access, please contact the repository owner ([@csdmichael](https://github.com/csdmichael)) with your GitHub username and a brief description of your use case.

---

## 9. Future Ideas — Extending the POC

The current solution is a working end‑to‑end POC. The following enhancements would harden it for broad enterprise rollout across security, governance, cost control, data protection, identity, and operations.

### 9.1 Security — Microsoft Defender for Cloud & Defender for AI

- Onboard the Fabric capacity, Foundry account, Storage, Key Vault, and App Service to **Microsoft Defender for Cloud** for continuous posture management (CSPM) and workload protection (CWPP).
- Enable **Defender for AI Workloads** on the Foundry account to detect prompt‑injection, data‑exfiltration, jailbreak, and wallet‑abuse patterns against the Operations, Recommendation, and Orchestration agents.
- Stream Defender alerts into a central Log Analytics workspace and surface them on the Admin / IT Operations dashboard alongside ML model health.
- Apply **Azure Policy** initiatives (Microsoft Cloud Security Benchmark, NIST 800‑53) at the Resource Group scope so every future deployment inherits the same guardrails.

### 9.2 AI Gateway — Azure API Management in Front of Foundry Agents

Put **Azure API Management (APIM) as an AI Gateway** between the web app and the Foundry agents / model endpoints to centralize governance:

- **Token limits & quotas** per user, per agent, and per cost center using the `llm-token-limit` / `azure-openai-token-limit` policies.
- **Cost tracking & chargeback** via `llm-emit-token-metric` / `azure-openai-emit-token-metric`, with prompt and completion tokens dimensioned by subscription, user, and cost center, then visualized in Azure Monitor workbooks.
- **Semantic caching** (`llm-semantic-cache-*`) to deflect repeat planner questions (e.g. *"show critical shortages at Plant 4"*) and cut Foundry inference cost.
- **Centralized governance** — single ingress for authN/Z, throttling, content safety, jailbreak detection, audit logging, and circuit‑breaking across all current and future agents.
- **Load balancing & failover** across multiple Foundry deployments / regions for resilience during launch‑week traffic spikes.

### 9.3 Data Protection — Microsoft Purview Sensitivity Labels & Row‑Level Security

- Apply **Microsoft Purview** sensitivity labels (e.g., *Confidential — Supply Chain*, *Restricted — Supplier Pricing*) to OneLake tables, Fabric items, and Foundry datasets so classification flows end‑to‑end.
- Enable **Row‑Level Security (RLS)** and **Object‑Level Security (OLS)** on the gold Lakehouse / semantic model so a planner only sees shortages for their plant(s) or program(s), and supplier‑sensitive columns (cost, contract terms) are masked for non‑sourcing roles.
- Wire the Fabric Data Agent to respect the caller's Entra identity so natural‑language Q&A inherits the same RLS rules — no bypass via the agent.
- Use **Purview DLP** policies to prevent labeled data from being copied into unmanaged exports or pasted into external LLMs.

### 9.4 Identity — Extend Entra ID Integration with App Manifest Metadata

Entra ID SSO is already implemented. Extend it with **App Registration manifest** metadata to drive enterprise lifecycle and chargeback:

- Populate **Application** and **Service Principal** custom security attributes (`costCenter`, `businessUnit`, `dataOwner`, `environment`, `program`) so every API/agent call can be attributed to a cost center.
- Use **optional claims** and **app roles** (`Planner`, `Sourcing`, `Admin`, `Executive`) to drive UI authorization and RLS scoping consistently from a single source of truth.
- Tag Azure resources (Foundry, APIM, App Service, Storage, Fabric capacity) with the same `costCenter` / `program` values so Azure Cost Management reports line up with Entra app metadata.
- Enforce **Conditional Access** (compliant device, MFA, named locations) on the app and on the Foundry / APIM endpoints.

### 9.5 Operations — Azure SRE Agent for 24×7 Monitoring

- Onboard the App Service, APIM, Foundry account, and Fabric workloads to the **Azure SRE Agent** for autonomous incident detection, triage, and remediation suggestions.
- Let the SRE Agent watch Application Insights / Log Analytics signals (latency, 5xx, token‑spend anomalies, ML drift alerts, Defender alerts) and open enriched incidents with probable root cause and suggested runbooks.
- Integrate SRE Agent findings into the Admin / IT Operations console alongside model‑performance dashboards so platform health and ML health are observed in one pane.
- Pair with **Azure Monitor Workbooks** and **Action Groups** for paging, and feed post‑incident learnings back into the deployment runbook in [docs/DEPLOYMENT_GUIDE.md](docs/DEPLOYMENT_GUIDE.md).

### 9.6 Other Candidate Extensions

- **Continuous evaluation** of all Foundry agents (groundedness, relevance, safety) on a scheduled batch, with results trended in the Admin console.
- **Multi‑tenant / multi‑program** isolation using Fabric workspace‑per‑program plus APIM products keyed by `program` claim.
- **Event‑driven retraining** triggered by drift detected in the Shortage Risk Calculator or Demand Forecaster.
- **Teams / Copilot Studio** front‑end so planners can chat with the Operations Orchestration Assistant from Microsoft Teams without leaving their workflow.
