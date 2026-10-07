---
name: docs
description: Use this skill when someone calls the Conduit API with their own API key. It covers integration, sandbox tests, and the full customer journey: onboarding, virtual accounts, crypto wallets, deposits, conversions, payouts, webhooks. It gives the location of the documentation, the OpenAPI spec and the discovery endpoints. Triggers are Conduit API, api.conduit.financial, api.sandbox.conduit.financial, Conduit sandbox, Conduit docs and Conduit API key.
---

# Conduit API

| Environment | Base URL | Money |
|---|---|---|
| production | `https://api.conduit.financial/v2` | real |
| production sandbox | `https://api.sandbox.conduit.financial/v2` | simulated. The `/sandbox/*` endpoints replace each wait with a call. |

The [authentication page](https://docs.conduit.financial/authentication) explains which API key fits which environment.

Build and test on the sandbox. Change the base URL for real customers. The API is the same.

## Documentation

- **Guides, concepts, sandbox recipes, errors, conventions, changelog:** `https://docs.conduit.financial`. The page index is `https://docs.conduit.financial/llms.txt`. Each page has a Markdown copy at its URL plus `.md`. `https://docs.conduit.financial/llms-full.txt` holds all pages in one file.
- **Exact request and response shapes:** `GET $BASE/api-docs/openapi.json`, with no auth. The spec matches the deployed build. The sandbox spec also documents each `/sandbox/*` endpoint.
- **What an environment supports now:** ask the API, not the docs. The [API reference](https://docs.conduit.financial/api-reference/overview) lists a requirements endpoint per resource, the offerable asset and chain pairs, and quotes.
- **Error codes and the error response shape:** `https://docs.conduit.financial/errors`.
- **Conventions** (ids, casing, pagination, idempotency, async responses): `https://docs.conduit.financial/api-reference/overview`.
- **Claude Code:** `claude mcp add --transport http conduit-docs https://v2.docs.conduit.financial/mcp` adds the documentation as an MCP server.

Read the guide before the first call of a phase. Read the spec before you build a request body.

## Setup

The API key is the only credential. The repository `README.md` says where to set it. Send it in the `x-api-key` header. Each path below is relative to the base URL. The API creates each resource under the organization of the key.

## The journey

Do the phases in order. Each later phase uses ids from an earlier phase. Each row gives the guide to read first and the sandbox recipe for the phase.

| Phase | Read first | Sandbox recipe |
|---|---|---|
| 1. Onboard a business customer (KYB) | [Onboard a customer](https://docs.conduit.financial/guides/onboard-customer), [KYB overview](https://docs.conduit.financial/kyb/overview) | [Customer KYC](https://docs.conduit.financial/sandbox/customer-kyc), [Example data](https://docs.conduit.financial/sandbox/example-data) |
| 2. Provision a virtual account and a crypto wallet | [Add virtual accounts](https://docs.conduit.financial/guides/add-virtual-accounts), [Add a crypto wallet](https://docs.conduit.financial/guides/add-crypto-wallet) | [Quickstart](https://docs.conduit.financial/sandbox/quickstart) |
| 3. Fund the customer | [Virtual accounts](https://docs.conduit.financial/concepts/virtual-accounts), [Receive crypto](https://docs.conduit.financial/guides/receive-crypto-lifecycle) | [Deposits](https://docs.conduit.financial/sandbox/deposits) |
| 4. Convert | [Convert](https://docs.conduit.financial/guides/convert), [Quote before you order](https://docs.conduit.financial/guides/quote-before-you-order) | [Conversions](https://docs.conduit.financial/sandbox/conversions), [ONRAMP](https://docs.conduit.financial/sandbox/onramps), [OFFRAMP](https://docs.conduit.financial/sandbox/offramps) |
| 5. Pay out | [Send a payout](https://docs.conduit.financial/guides/send-payout), [Whitelist recipients](https://docs.conduit.financial/concepts/whitelist-recipients), [Registered addresses](https://docs.conduit.financial/concepts/registered-addresses) | [Withdrawals](https://docs.conduit.financial/sandbox/withdrawals) |
| Webhooks (any phase) | [Webhooks](https://docs.conduit.financial/webhooks), [Signature verifier](https://docs.conduit.financial/webhook-verifier) | [Cheat sheet](https://docs.conduit.financial/sandbox/cheat-sheet) |

Call the requirements endpoint of the phase before each write. Build the body from the answer. Then submit.

## Conventions

- Responses are gzip. Always use `curl --compressed`.
- Send an `Idempotency-Key` on each POST that creates or moves a resource. Use one key per operation and send the same key on each retry of that operation. A new key starts a new operation. The conventions page gives the rules.
- Many writes return `202` and complete later. Poll the `GET` of the resource until the status is terminal. On the sandbox, call the endpoint from the recipe instead.
- Capture the response, then filter it: `printf '%s' "$VAR" | jq …`. In zsh, `echo` changes captured JSON.
