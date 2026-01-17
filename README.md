# Agentic AI CC

This repository contains two demo applications:

- `athena-agentic-unified-desktop/` — Athena Agentic Unified Desktop (agent UI + API + SSE stream)
- `agentic-cx-demo/` — Agentic CX Demo (customer chat UI + proxy to Athena)

## Quick start (local)

1) Start Athena:

- `cd athena-agentic-unified-desktop`
- `npm install`
- `npm start`
- Open: `http://localhost:3001/?cust=GB13820473`

2) Start CX Demo:

- `cd agentic-cx-demo/server`
- `npm install`
- `npm start`
- Open: `http://localhost:4002/?cust=GB13820473&athena=http://localhost:3001&reset=1`
