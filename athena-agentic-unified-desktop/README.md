# Athena Cognitive Desktop (Agentic Unified Desktop)

A modular, real-time SPA for contact-center agents. It serves a web UI and an API that orchestrates insights with either OpenAI/Azure OpenAI or Neuro® SAN, and streams Customer 360 updates to clients via Server-Sent Events (SSE).

## Quick start

Prereqs:
- Node.js 18+ and npm
- macOS/Linux/Windows

Setup and run:

```bash
# In Agentic Unified Desktop/
npm install

# Configure environment (edit .env — do not commit secrets)
# Then start the server
npm run start:debug
# or
npm start
```

Open the desktop UI:
- http://localhost:3001/

Tip: Append `?cust=random` to pick a demo customer ID at random.

## Environment

Set these in `.env` (examples only; replace with your own values):

- ENDPOINT_URL: Azure OpenAI/OpenAI base URL (e.g., https://your-aoai.openai.azure.com/)
- DEPLOYMENT_NAME: Model deployment (e.g., gpt-4o)
- AZURE_OPENAI_API_KEY: Your API key (keep private)
- AZURE_OPENAI_API_VERSION: Optional; defaults to 2025-01-01-preview in code
- AGENT_ACTIONS_PROVIDER: Default orchestration provider when UI doesn’t supply one. Options: `openai` | `neurosan`
- NEUROSAN_BASE_URL: Base URL for Neuro® SAN (e.g., http://localhost:8080)
- NEUROSAN_USE_STREAMING: `true` | `false` (use `/streaming_chat` when true)
- NEUROSAN_NETWORK and NEUROSAN_NETWORK_*: Default/override network names for widgets
- LLM_REQUEST_TIMEOUT_MS: Default LLM timeout (ms)
- NEUROSAN_TIMEOUT_MS: Neuro-SAN request timeout (ms)
- NEUROSAN_EXEC_SUMMARY_MAX: Max chars for execution summaries
- SUPPRESS_AUTO_BOT_REPLY: Set to `true` to omit server-generated `botReply` in `/external-chat` responses and SSE
- PORT: Optional (default 3001)

Security note: never commit real API keys.

## UI behavior

- Active customer ID can be provided via the URL: `?cust=<ID>` or `?cust=random`.
- Demo ID pool: `GB26669607`, `GB13820473`, `GB22446688`, `GB77553311`, `GB99001234`, `GB11002233`, `GB22003344`, `GB33004455`, `GB44005566`, `GB55006677`, `GB66007788`, `GB77008899`, `GB88009900`, `GB99112233`, `GB12345098`.
- “Agent Network Actions” lets you switch orchestration engine (Neuro‑SAN vs OpenAI).
- SSE pushes keep the UI updated with Customer 360, sentiment/risk, NBA, and agent replies.

## API and streaming

Base path: `/api/v1`

- POST `/api/v1/get-insights`
  - Body: `{ customerId, conversationHistory?, requestedWidgets: string[], extraVarsMap?, providerMap? }`
  - Returns: a map keyed by widget name with normalized JSON payloads

- POST `/api/v1/external-chat`
  - Body: `{ customerId, message }`
  - Returns: `{ customer360, wordcloud, insights, botReply?, traceId }`
  - Notes: If `SUPPRESS_AUTO_BOT_REPLY=true`, `botReply` is omitted
  - Side effects: broadcasts the payload over SSE to subscribers of this customer

- GET `/api/v1/stream/customer-360/:id` (SSE)
  - Streams the latest snapshot on connect and subsequent updates. Examples:
    - External chat update: `{ customer360, wordcloud, insights, botReply?, traceId }`
    - Agent reply: `{ type: "agentReply", customerId, message, traceId, ts }`

- POST `/api/v1/agent-reply`
  - Body: `{ customerId, message }`
  - Emits an `agentReply` SSE event

- GET `/api/v1/customer-360/:id`
  - Returns the last known snapshot

- POST `/api/v1/reset-customer`
  - Body: `{ customerId }`
  - Clears in-memory conversation history, executed actions, last snapshot, and disconnects SSE clients for that customer
  - Optional protection: if `RESET_TOKEN` is set in the environment, you must send header `x-reset-token: <RESET_TOKEN>`

## Pairing with the CX Demo

Run the CX Demo (see its README), then open:
- http://localhost:4002/?athena=http://localhost:3001&cust=random

The CX demo forwards customer messages to `/api/v1/external-chat` and subscribes to `/api/v1/stream/customer-360/:id`.

## Troubleshooting

- No updates? Start with `npm run start:debug` and check logs.
- Duplicate replies? Use `traceId` to de-duplicate on the client.
- Not seeing `botReply`? Ensure `SUPPRESS_AUTO_BOT_REPLY` is unset/false.
- CORS: Enabled; if embedding elsewhere, confirm allowed origins.

## License

MIT
## Azure App Service (Deploy from GitHub)

This repo includes a GitHub Actions workflow that deploys the app to an Azure App Service (Linux).

1) Create an Azure Web App (Linux) for Athena
- Runtime: Node.js 18+
- App Settings: set your LLM-related environment variables (see **Environment** above)

2) Configure GitHub Actions authentication (recommended: OIDC)
- Create an Entra ID App Registration (service principal) for GitHub Actions
- Add a Federated Credential for your GitHub repo/branch (subject like: `repo:OWNER/REPO:ref:refs/heads/main`)
- Assign the service principal a role that can deploy to the Web App (e.g., Contributor scoped to the Web App or resource group)

3) Add GitHub repo secrets
The workflow expects:
- `AZURE_CLIENT_ID`
- `AZURE_TENANT_ID`
- `AZURE_SUBSCRIPTION_ID`
- `AZURE_WEBAPP_NAME_ATHENA` (the Web App name, e.g., `athena-desktop-prod`)

4) Deploy
- Push to `main` (or run the workflow manually from the Actions tab).

5) Use it with the CX Demo
- Once deployed, your Athena URL will look like `https://<app-name>.azurewebsites.net`
- Pass that base URL into the CX Demo as the `athena` query parameter.

Notes
- SSE endpoint: `GET /api/v1/stream/customer-360/:id` (works on App Service). If you later add Front Door/CDN, ensure the SSE path is not cached or buffered.
