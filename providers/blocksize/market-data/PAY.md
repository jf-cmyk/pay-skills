---
name: market-data
title: "Blocksize Market Data"
description: "Crypto, supported equity, FX, and metals observations, VWAP windows, market briefs, and provenance receipts through x402-paid HTTP, with free instrument discovery and readiness checks."
use_case: "Use for market-price lookups, bid/ask comparisons, multi-asset snapshots, and read-only pre-trade checks. Resolve symbols and product readiness first, then request only the observations the user needs."
category: finance
service_url: https://mcp.blocksize.info
openapi:
  path: openapi.json
---

Blocksize Market Data provides free discovery and accountless x402-paid HTTP
access. The adjacent OpenAPI snapshot contains 17 paid operations and six free
discovery/readiness operations, aligned with production v0.6.16.

Use `/v1/search?q=BTC` or `/v1/instruments/{service}` to find supported identifiers.
Both accept `limit` and `offset`; a page of results is not the full catalog.
Use `/v1/products`, `/v1/coverage`, `/v1/capabilities/check`, and `/v1/cache/status`
to inspect product coverage and readiness. These endpoints do not purchase data
and their catalog entries are not live quotes.

The paid routes use real OpenAPI path parameters: supply the discovered `pair`
or `ticker`, not a string substitution in a fixed BTC sample path. Coverage is
service-specific; a symbol supported by one product may not support another.
The seven single-symbol routes cover VWAP, bid/ask, AMM state, the latest completed
30-minute close, fixed 24-hour VWAP, FX, and metals. The 30-minute close is not a
rolling 30-minute VWAP. Cached observations are subject to freshness and readiness.

For an explicitly requested multi-symbol lookup, `/v1/batch` accepts a `reqs`
query such as `vwap:BTCUSD,bidask:ETHUSD`. Nine POST operations provide market
briefs, pre-trade checks, price receipts, macro snapshots, one-shot monitor
evaluations, token-quality/state-divergence indicators, and signal bundles.
The sidecar includes bounded example request bodies for each.

Preserve the timestamps, provider context, methodology, and quality/error flags
returned by the selected product. A price receipt is a provenance record, not a
cryptographically signed attestation. No RWA research feed is advertised here as
production-promoted, and catalog inclusion alone is not a live-delivery guarantee.

## Access and discovery

- Direct HTTP returns x402 v2 payment requirements for USDC on Solana mainnet
  and Base mainnet. This Pay.sh entry is validated for Solana; Base support in the
  API does not imply support in every Pay.sh client.
- Eligible starter credits belong to authenticated connector identities only.
  Anonymous Pay.sh HTTP requests cannot claim or spend connector credits.
- Free public MCP discovery: https://mcp.blocksize.info/mcp/server/
- API/AI catalogs: https://mcp.blocksize.info/.well-known/api-catalog and
  https://mcp.blocksize.info/.well-known/ai-catalog.json
- Agent instructions: https://mcp.blocksize.info/.well-known/agent-skills/index.json
- Authentication guide: https://mcp.blocksize.info/auth.md
- First observation guide: https://mcp.blocksize.info/quickstart/first-price

## Spend-aware usage

- Search available instruments before making a paid market-data request.
- Prefer one-symbol calls for exploratory tasks.
- Use `/v1/batch` only when the user needs several prices in the same workflow.
- Reuse returned symbols exactly instead of guessing unsupported pair formats.
- Treat the live 402 challenge as authoritative for price, network, asset, and
  recipient. Obtain the user's payment authorization before signing.
- Do not send wallet private keys or seed phrases to this API.
- Avoid polling unless the user has explicitly approved repeated paid calls.
- A monitor evaluation is a single request, not a subscription or background job.
