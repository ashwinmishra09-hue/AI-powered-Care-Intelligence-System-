AI-Powered Care Intelligence System
A decision-making intelligence layer for home healthcare — covering care delivery, operations, and patient engagement. Built for India-first deployment with global expansion readiness.
---
What this is
The Care Intelligence System is a fully interactive prototype dashboard that simulates how an AI agent operates across a home healthcare company's three core domains:
Care delivery — real-time risk alerts, AI-generated care actions, visit tracking, diagnostic pipeline
Operations — nurse utilisation, route optimisation insights, supply inventory, billing pipeline, nurse performance
Patient engagement — engagement feed, churn prediction, upsell opportunities, multilingual AI messaging
AI intelligence layer — live data ingestion status, model performance metrics, autonomous decision log
The system uses the Anthropic Claude API to generate live AI patient summaries and care actions directly in the browser.
---
Live features
Feature	Description
Risk scoring	Weighted composite score across BP, SpO2, glucose, medication adherence, engagement
AI risk alerts	Colour-coded patient alerts sorted by severity with action urgency tags
AI care actions	Ranked, assignee-specific recommended actions generated per patient
Patient engagement feed	Real-time timeline of WhatsApp messages, AI reminders, churn flags
Nurse utilisation	Live utilisation bars per nurse with visit counts
Decision log	Every autonomous AI decision with confidence score and execution status
Multilingual messaging	AI messages tracked across Hindi, Telugu, Tamil, Kannada, English
---
Project structure
```
care-intelligence-system/
├── index.html          ← Complete prototype (HTML + CSS + JS in one file)
└── README.md           ← This file
```
---
Quick start
Option 1 — Open directly in browser
Download `index.html`
Open it in Chrome, Edge, Safari, or Firefox
The dashboard loads immediately with all four modules active
Navigate between Care delivery / Operations / Patient engagement / AI intelligence using the top tab bar
> The dashboard works fully without an API key. The AI summary generator on individual patient cards requires an Anthropic API key (see below).
Option 2 — Deploy to GitHub Pages (recommended for sharing)
Create a new GitHub repository
Upload `index.html` as the only file — rename it `index.html` if needed
Go to Settings → Pages → Source: main branch / root
Your live URL will be: `https://YOUR-USERNAME.github.io/REPO-NAME/`
---
Enabling live AI summaries
The system calls the Anthropic Claude API for live patient summaries. To enable:
Get an API key from console.anthropic.com
Open `index.html` in a text editor
Find the line: `const API_KEY = "YOUR_API_KEY_HERE";`
Replace with your actual key: `const API_KEY = "sk-ant-api03-...";`
Save and reload
> **Security note:** For production deployment, never expose API keys in client-side HTML. Route API calls through a backend proxy. This prototype is for demonstration and internal use only.
---
Architecture overview
```
Input sources
  WhatsApp Business API · IoT vitals stream · Nurse voice notes
  Lab results API · CRM/EHR · Call transcripts
        ↓
MCP connector layer
  Standardised tool connectors for each data source
        ↓
Patient Memory Layer (unified FHIR-compliant graph)
  Longitudinal record per patient — all signals merged
        ↓
AI Reasoning Layer (Claude Sonnet)
  Risk scorer → Flag generator → Action planner → Summary writer
        ↓
AI Agent Orchestration
  Triage agent · Escalation agent · Engagement agent · Documentation agent
        ↓
Agent Interface
  Coordinator dashboard · Nurse mobile app · Approval gates · Audit trail
        ↓
Outputs
  Nurse dispatch · WhatsApp alerts · Risk updates · Clinical summaries · Insurance claims
```
---
AI agent actions
The system implements 12 distinct AI agent actions across four domains:
Care delivery (automated)
Risk score engine — Continuous weighted scoring across 5 signal dimensions every 15 minutes
Clinical escalation — Auto-dispatch nurse + page coordinator when score ≥ 80
Diagnostic interpreter — Auto-reads lab reports, flags abnormals, sends family WhatsApp summary
Operations (mix of auto and human-in-loop)
Route optimiser — Replans nurse routes every 30 minutes using live traffic
Nurse dispatcher — Ranks and suggests nurse assignment per visit (human confirms)
Supply forecaster — Predicts stockout risk and generates purchase orders (human approves)
Patient engagement (mix)
Engagement companion — Sends personalised multilingual messages on schedule (auto)
Churn predictor — Flags patients likely to leave 14–21 days in advance (human acts)
Upsell recommender — Identifies service upgrade opportunities with ROI case (human delivers)
Intelligence layer (automated)
Documentation AI — Converts voice notes to SOAP notes + ICD-10 coded claims
Decision logger — Logs every AI decision with inputs, confidence, and outcome
Patient memory layer — Continuously updates longitudinal patient graph from all sources
---
APIs used
API	Endpoint	Primary attributes
WhatsApp Business	`graph.facebook.com/v18.0/{phone_id}/messages`	patient_phone, message_type, language_code, delivery_status, response_latency_min
IoT Vitals Stream	`/api/v1/vitals/stream/{device_id}`	systolic_bp, diastolic_bp, heart_rate, spo2, blood_glucose, anomaly_flag
Nurse Notes	`/api/v1/visits/{visit_id}/notes`	raw_transcript, subjective, objective, assessment, plan, icd10_codes, protocol_score
Lab Results	`/api/v1/diagnostics/results/{order_id}`	test_name, result_value, abnormal_flag, trend_direction, family_notified
Anthropic Messages	`api.anthropic.com/v1/messages`	model, system, tools[], tool_choice, confidence_score, stop_reason
Triage Output	`/api/v1/triage/results`	risk_score, risk_tier, flags[], actions[], ai_summary, confidence_score, human_override
---
MCP (Model Context Protocol) servers
Five MCP tool servers are registered with the Anthropic API as callable tools:
`MCP WhatsApp server` — `get_whatsapp_history(patient_id)`
`MCP IoT server` — `get_vitals_stream(device_id)`
`MCP EHR server` — `get_patient_record(fhir_id)`
`MCP Lab server` — `get_lab_results(order_id)`
`MCP Nurse notes server` — `get_nurse_notes(visit_id)`
The agent uses `tool_choice: auto` — Claude decides which tools to call based on context gaps. When `stop_reason` returns `tool_use`, the agent executes the tool and passes results back before the final reasoning pass.
---
Dummy data
The prototype ships with 6 realistic Indian home healthcare patients:
Patient	Age	Condition	Risk tier
Ramesh Iyer	68	Type 2 Diabetes, Hypertension	Critical (91)
Lakshmi Venkat	74	COPD, Heart Failure	Critical (88)
Suresh Naidu	55	Post-surgical recovery	Critical (82)
Meena Krishnan	62	Stroke rehabilitation	High (74)
Arjun Pillai	45	Hypertension, Obesity	High (68)
Savitha Rao	70	Chronic Kidney Disease	Medium (55)
---
Extending the prototype
Add a real patient
Edit the `patients` array in `index.html`. Each patient object follows this schema:
```javascript
{
  id: 7,
  name: "Patient Name",
  age: 60,
  gender: "M",
  condition: "Condition 1, Condition 2",
  nurse: "Nurse Name",
  lastVisit: "Yesterday",
  vitals: {
    bp: "140/90", bpS: "warn",
    hr: "88",     hrS: "ok",
    spo2: "96%",  spo2S: "ok",
    glucose: "Normal", glucoseS: "ok"
  },
  adherence: 75,
  engagement: 65,
  flags: ["Flag description 1", "Flag description 2"],
  flagLevels: ["warn", "warn"],
  actions: ["Action 1", "Action 2"],
  actionUrgency: ["today", "week"]
}
```
Adjust risk weights
Open Settings in the dashboard and use the weight sliders. Weights recalculate all risk scores in real time. Settings persist for the session.
Connect a real backend
Replace the `generateSummary()` function's fetch call with your own backend endpoint that proxies to the Anthropic API. This removes the client-side API key exposure.
---
Business context
This system was designed for a home healthcare company (India-first) providing:
At-home nursing
Chronic care management
Diagnostics
Remote patient monitoring
Expected impact
Metric	Target
Unplanned hospitalisations	Down 20–30%
Nurse travel time	Down 25–35%
Documentation time per visit	12 min → 3 min
Patient retention (12-month)	Up 15–20%
Care coordinator response time	Down 60%
Insurance claim rejection rate	Down 18%
---
Tech stack
Layer	Technology
Frontend	Vanilla HTML, CSS, JavaScript — zero dependencies
AI reasoning	Anthropic Claude Sonnet (claude-sonnet-4-20250514)
Data protocol	MCP (Model Context Protocol) tool connectors
Health data standard	FHIR-compliant patient graph
Deployment	GitHub Pages (static) or any web server
Patient communication	WhatsApp Business API
Vitals ingestion	IoT device REST stream
---
Roadmap (production path)
```
Phase 1 (0–3 months)    Patient Memory Layer + Clinical Documentation AI
Phase 2 (3–9 months)    Risk Prediction Engine + Vital Anomaly Detector + Nurse Copilot
Phase 3 (9–18 months)   Engagement Companion + Route Optimiser + Care Coordinator AI
Phase 4 (18–30 months)  Chronic Progression Tracker + Churn Engine + Payer Automation
```
---
Licence
This prototype is for internal demonstration and product development purposes.
---
Built with Claude Sonnet · Anthropic API · WhatsApp Business API · FHIR · MCP
