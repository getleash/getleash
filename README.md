# Leash

**Accounts for Agents.** Your AI agent gets its own scoped USDC account on Base — not your credit card, not a shared API key. Set a budget, watch it work, revoke it on-chain in one transaction if it goes rogue.

Leash is an MCP proxy that lets a Claude Code agent pay for x402-enabled APIs (CoinMarketCap first, more upstreams to follow) using its own per-agent ERC-4337 sub-account. The agent holds the wallet, not the vendor. Limits are policy you write in markdown, enforced by an on-chain validator and a local audit log.

## Why this exists

Today, when an AI agent needs to pay for something, the developer either hardcodes their personal card, hands over a shared API key, or builds a custom billing relationship with each vendor. All three are wrong: blast radius is your wallet, revocation requires a phone call, and "delegated" usually means "indistinguishable from you."

The argument behind Leash is simple: **the agent should hold the wallet, not the vendor.** A per-agent sub-account with its own funds, its own session key, and a written policy is the right shape of authority for a non-human worker. When the agent goes rogue, the blast radius is the sub-account's balance — bounded by what you topped up, revocable in one signed transaction.

## How it works

- **One sub-account per agent**, deployed via ERC-4337 on Base mainnet using Kernel v3.3. Verified live: a session-key-signed EIP-3009 `transferWithAuthorization` accepted by USDC on Base ([tx `0x5000306a…`](https://basescan.org/tx/0x5000306aREPLACE_WITH_FULL_HASH)).
- **A session key**, scoped by an ERC-7579 `SessionKeyValidator` with an on-chain allowlist — target, selector, per-tx amount, and expiry are enforced by the validator itself, not by Kernel's root. Foundry-verified on a Base mainnet fork.
- **A markdown policy file** at the agent's repo root — daily/weekly/monthly caps, recipient allowlist, drain-to-hub recovery — enforced by Leash before signing and logged to local SQLite.
- **MCP transparent proxy** for x402 upstreams. The agent calls `coinmarketcap__get_price`; Leash sees the HTTP 402, signs an EIP-3009 authorization with the session key, retries. The agent never sees the payment dance. Verified end-to-end against CoinMarketCap via Coinbase CDP facilitator.

## Status

Pre-launch. Mainnet demo and v0 release coming soon.

## Repositories

- `getleash/leash` — MCP server, CLI, contracts. *(Public release coming with launch.)*
- `getleash/.github` — org-level templates and this README.

## Read more

- Site — [getleash.dev](https://getleash.dev) *(coming with the launch essay)*
- Essay — *"I gave my AI agent its own bank account this morning"* *(forthcoming)*
- Founder — Stepan Kouba on [LinkedIn](https://www.linkedin.com/in/REPLACE_WITH_HANDLE)

---

> An AI agent can now hold its own scoped, revocable spending authority — without you handing over your card or your API key. When the agent goes rogue, the blast radius is the sub-account's balance, not your bank account.
