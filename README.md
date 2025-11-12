# Flowency Client Documents

This repository stores client PRD documents for the Flowency portal.

## Structure

```
clients/
  {clientId}/
    prd.md          # Main product requirements document
    notes.md        # Optional: Additional notes
    assets/         # Optional: Images, diagrams, etc.
```

## Current Clients

- **NinerOps** (`clients/ninerops/`)

## Access

This repository is accessed programmatically by the Flowency Portal application via GitHub API.

Documents are edited by clients through the portal interface at: https://portal.flowency.co.uk

## Adding a New Client

1. Create folder: `clients/{clientname}/`
2. Add initial `prd.md` file
3. Commit and push
4. Create invite token in DynamoDB
5. Send magic link to client

---

**Managed by:** Flowency Team
**Portal Repo:** https://github.com/flowency-live/portal
