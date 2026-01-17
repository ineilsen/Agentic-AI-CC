# Athena Agentic Unified Desktop — Demo Script (Marketing)

## Purpose (what the audience should feel)
Athena turns a live customer conversation into a guided resolution journey—**plan → execute → explain → respond**—in real time. Powered by **Neuro SAN** (Cognizant’s multi-agent orchestrator), Athena choreographs specialized agents to produce structured actions, validated outcomes, and customer-ready responses—at conversation speed.

## Audience promise (one-liner)
“Watch how Athena + Neuro SAN transforms a messy customer issue into a clear, confident resolution—live.”

---

## Setup & run (local)

### 1) Start Athena (Agentic Unified Desktop)
- From the Athena folder:
  - `npm install`
  - `npm start`
- Open Athena:
  - `https://athena-desktop-app.azurewebsites.net/?cust=GB77553311`

### 2) Start CX Demo (Customer UI)
- From `Agentic CX Demo/server/`:
  - `npm install`
  - `npm start`
- Open CX Demo (explicit customer + clean start):
  - `https://agentic-cx-demo-1768502151.azurewebsites.net/?cust=GB77553311&athena=https://athena-desktop-app.azurewebsites.net&reset=1`

---

## Setup & run (Azure / deployed)

### URLs you will use
- Athena (example): `https://athena-desktop-app.azurewebsites.net/`
- CX Demo (example): `https://agentic-cx-demo-1768502151.azurewebsites.net/`

### 1) Open Athena on the demo customer
- `https://athena-desktop-app.azurewebsites.net/?cust=GB77553311`

### 2) Open CX Demo pointing to Athena + forcing a clean start
- `https://agentic-cx-demo-1768502151.azurewebsites.net/?cust=GB77553311&athena=https://athena-desktop-app.azurewebsites.net&reset=1`

---

## How customer identity works (keep this consistent)
- The “customer identity” for the demo is the `cust` URL parameter.
- CX Demo uses `cust` to:
  - send customer messages to Athena (`POST /api/v1/external-chat` via the CX Demo server proxy)
  - subscribe to Athena’s SSE stream (`GET /api/v1/stream/customer-360/:cust`)
- Athena Desktop uses `?cust=...` to focus the agent UI on the same customer.

**Rule for smooth demos:** use the **same explicit** `cust` value in **both** Athena and CX Demo URLs.

---

## Operator notes (for consistent demos)

### Always start with a clean customer state
- Always use CX Demo with `reset=1`.
- Why: Athena keeps demo state in memory keyed by `customerId`. Refreshing the page does not clear it.
- Result: you avoid “unscripted” carryover from earlier runs.

### If you need a guaranteed fresh start mid-demo
- Reload CX Demo with the same `cust` and `reset=1`.
- Example:
  - `...?cust=GB13820473&athena=https://<athena-app>.azurewebsites.net&reset=1`

### Avoid `cust=random` for on-stage demos
- `cust=random` is chosen **client-side**; refreshes can change the customer ID.
- On stage, always use an explicit ID (e.g., `GB13820473`) for repeatability.

### Keep the narration anchored to four “wow pillars”
- **Real-time streaming (SSE):** “updates arrive live, no polling.”
- **Neuro SAN orchestration:** “specialist agents coordinated, not a single prompt.”
- **Structured actions:** “plan, validate, execute—traceable and consistent.”
- **Customer-ready responses:** “clear, empathetic messages drafted instantly.”

---

## Demo flow (8–10 minutes)

### 0) Pre-flight (30 seconds, before you present)
- Confirm both tabs are open:
  - Athena: `...?cust=GB13820473`
  - CX Demo: `...?cust=GB13820473&reset=1&athena=...`
- Send one quick test message from CX Demo (“hello”).
- Verify Athena updates and CX Demo receives streamed replies.

### 1) Opening (30 seconds)
Say:
- “Athena Agentic Unified Desktop is a real-time, agent-first workspace that turns live conversations into decisive guidance.”
- “Powered by Neuro SAN—Cognizant’s multi-agent orchestrator—it choreographs agent actions end-to-end: plan → execute → explain → respond.”

### 2) Set the scene (30–45 seconds)
Show:
- Athena Desktop (agent view)
- CX Demo (customer view)

Say:
- “On the right is the customer. On the left is the agent workspace. As the conversation unfolds, Athena continuously streams Customer 360 context, risk signals, next-best actions, and response drafts.”

### 3) Customer starts the issue (60–90 seconds)
In CX Demo, paste:
- “My broadband drops every evening. I’ve rebooted twice and it’s still happening. I work from home—please fix this.”

Say while the UI updates:
- “Notice the experience: no waiting for a manual ‘case creation’ step. The moment the customer speaks, Athena updates the Customer 360 picture and surfaces what matters.”

Call out:
- Customer 360 updates
- Mini insights / summary
- Any health/risk indicators

### 4) The ‘wow’ moment: Neuro SAN action choreography (2 minutes)
In Athena, open the **Agent Network Actions** section.

Say:
- “This is where Neuro SAN shines. It doesn’t just generate tips—it orchestrates a sequence of investigative actions.”
- “These are structured actions with rationale and traceability—designed to move the conversation toward resolution.”

### 5) Execute an action live (2 minutes)
Pick one action that fits your story (examples):
- Check local outage / maintenance
- Run stability diagnostics
- Review recent cases / SLA risk

Say:
- “I’m executing this action. Neuro SAN coordinates the right specialist agent, validates the best path, and returns structured findings we can trust.”

Call out:
- Summary + findings
- Confidence / clarity of output
- Updated next-best action after execution

### 6) Explain + send a customer-ready response (90 seconds)
Say:
- “Now Athena does the hardest part: translating internal findings into an empathetic, customer-ready message—without exposing internal tool noise.”

Send a response from Athena to the customer.

Call out:
- “It explains what we found, what we’re doing, and what the customer can do next—clearly and calmly.”

### 7) Real-time sync (45–60 seconds)
Show CX Demo receiving the agent reply live.

Say:
- “This is real-time streaming. The customer experience updates instantly via SSE—no refresh, no copy/paste.”

### 8) Close (30–45 seconds)
Say:
- “Athena reduces handle time, improves consistency, and keeps agents confident under pressure.”
- “Powered by Neuro SAN, it turns a conversation into a guided journey—plan → execute → explain → respond—at conversation speed.”

---

## Backup scenarios (if you want variety)

### A) De-escalation / retention risk
Customer message:
- “This is the third time. I’m ready to cancel.”

Narration angle:
- “Athena detects risk and recommends a retention-safe next best action plus a calm, empathetic response.”

### B) Billing shock
Customer message:
- “My bill jumped this month and I don’t understand why.”

Narration angle:
- “Athena pulls billing context into Customer 360, explains the likely driver, and drafts a customer-ready explanation with next steps.”
