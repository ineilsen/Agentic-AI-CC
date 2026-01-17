# Agentic CX Demo

A simple customer web UI that proxies messages to the Athena Cognitive Desktop and listens for real-time updates via SSE. Useful for demonstrating end-to-end agent/customer flows.

## Quick start

Prereqs:
- Node.js 18+ and npm

Setup and run the server:

```bash
# In Agentic CX Demo/server/
npm install
npm start
(base) m_379625@AMBGB000697 server % COMPANY_NAME="Acme" node app.js
```

Open the customer UI:
- http://localhost:4002/
- Recommended with parameters: `http://localhost:4002/?cust=GB13820473&athena=http://localhost:3001`
  - `athena` points to the Athena Desktop base URL
  - `cust` selects the customer ID (`random` picks from a demo pool)

## How it works

- The client subscribes to Athena’s SSE stream at `/api/v1/stream/customer-360/:id` using `EventSource`.
- When the user sends a message, the server forwards it to Athena via `POST /chat/send`, which proxies to Athena `/api/v1/external-chat`.
- The client de-duplicates streamed replies using `traceId` and shows an immediate HTTP fallback reply if present.
- An initial on-open welcome bubble is rendered client-side for UX; it’s independent of Athena.

## API (server)

- POST `/chat/send`
  - Body: `{ customerId, message, athenaBaseUrl }`
  - Forwards to `{athenaBaseUrl}/api/v1/external-chat` and returns the JSON response.

## Customer IDs and params

- Demo ID pool: `GB26669607`, `GB13820473`, `GB22446688`, `GB77553311`, `GB99001234`.
- URL params:
  - `athena`: Base URL to Athena Desktop (default `http://localhost:3001`)
  - `cust`: Specific ID or `random`
  - `reset`: If `true`/`1`, clears Athena server-side chat state for the selected customer on page load (useful for repeatable demos)

## Pairing with Athena Desktop

Start Athena Desktop first:
- http://localhost:3001/

Then open the CX Demo with:
- http://localhost:4002/?athena=http://localhost:3001&cust=random

## Troubleshooting

- No responses? Ensure Athena Desktop is running and reachable at the `athena` URL.
- Seeing two replies? The client deduplicates by `traceId`; if you also display the HTTP `botReply`, ensure it’s not suppressed or shown twice.
- To suppress server-generated bot replies at source, set `SUPPRESS_AUTO_BOT_REPLY=true` in Athena’s `.env`.

## License

MIT

## Azure App Service (Deploy from GitHub)

This repo includes a GitHub Actions workflow that deploys the CX Demo to an Azure App Service (Linux).

1) Create an Azure Web App (Linux) for the CX Demo
- Runtime: Node.js 18+
- Set an App Setting (optional): `COMPANY_NAME=Acme`

2) Set the startup command (important)
The CX Demo server lives in `server/` and serves static files from `client/public` via a relative path.

In the Web App Configuration, set **Startup Command** to:
- `cd server && npm start`

3) Configure GitHub Actions authentication (recommended: OIDC)
- Create an Entra ID App Registration (service principal) for GitHub Actions
- Add a Federated Credential for your GitHub repo/branch (subject like: `repo:OWNER/REPO:ref:refs/heads/main`)
- Assign the service principal a role that can deploy to the Web App (e.g., Contributor scoped to the Web App or resource group)

4) Add GitHub repo secrets
The workflow expects:
- `AZURE_CLIENT_ID`
- `AZURE_TENANT_ID`
- `AZURE_SUBSCRIPTION_ID`
- `AZURE_WEBAPP_NAME_CX_DEMO` (the Web App name)

5) Open the deployed CX Demo
- Your CX Demo URL will look like `https://<app-name>.azurewebsites.net/`
- Pass your deployed Athena URL in the `athena` query parameter, for example:
  - `https://<cx-app>.azurewebsites.net/?cust=GB13820473&athena=https://<athena-app>.azurewebsites.net`

Notes
- The customer browser connects directly to Athena’s SSE stream at `GET /api/v1/stream/customer-360/:id`, so Athena must be publicly reachable and allow CORS (Athena enables CORS by default).
