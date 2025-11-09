 # Artha Network White Paper

## Abstract

Artha Network is a decentralized, AI-assisted escrow protocol that makes peer-to-peer transactions feel as safe as buying from a major marketplace. Funds are locked in smart contracts, disputes are triaged by an AI arbiter with human fallback, and evidence lives on immutable storage. The goal is a low-friction, low-cost trust layer for everyday deals such as used-vehicle sales, rentals, and freelance milestones.

---

## 1. Problem Statement

P2P commerce keeps growing while trust keeps shrinking. Informal trades and gig payments are riddled with fraud, chargebacks, and unclear recourse. Traditional escrow is priced and designed for high-value deals, not a $50 marketplace pickup or a $300 gig milestone. People need a non-custodial, affordable, and simple way to transact with strangers.

---

## 2. Protocol Overview

**Artha Network** provides a non-custodial escrow with AI-assisted dispute resolution.

* **Non-custodial custody**: Funds are held in program-owned vaults. Neither platform operators nor arbitrators can seize them without authorized state transitions.
* **AI-assisted arbitration**: An off-chain arbiter service evaluates evidence bundles and issues a cryptographically signed ResolveTicket. A human reviewer can override during appeal windows.
* **Stablecoin rails**: USDC for price stability and global reach.
* **Accessible UX**: Link-first flows via Solana Actions and Blinks. Gas sponsorship removes the need for first-time SOL funding.
* **Affordable and scalable**: Cheap L1 throughput and off-chain AI keep per-deal costs low so micro-escrow remains viable.

---

## 3. Architecture

### 3.1 On-chain layer

* **Solana program** written in Rust with Anchor IDL.
* **State machine** for the deal lifecycle: `INIT → FUNDED → DELIVERED → DISPUTED → RESOLVED → RELEASED/REFUNDED`.
* **Program-owned vaults** for custody of USDC (SPL Token-2022).
* **Price snapshotting** using Pyth oracles at funding time.
* **Signature gating** for resolution execution. Only allow-listed arbiter keys or human override keys can finalize.

### 3.2 Off-chain layer

* **AI Arbiter Service** evaluates context plus evidence and signs a ResolveTicket (final action, rationale CID, confidence).
* **Evidence storage** on Arweave and IPFS. Only CIDs are recorded on-chain.
* **Actions and Blinks endpoints** return ready-to-sign transactions and support fee-payer policy.
* **Notifications and jobs** manage deadlines, reminders, and escalations.

### 3.3 Evidence, resolution, reputation

* **Evidence** is uploaded off-chain, hashed, virus-scanned, dual-pinned, and referenced by CID on-chain.
* **Resolution** occurs when the program verifies the ResolveTicket signature and enforces release, refund, or split.
* **Reputation** records attestations after each deal, enabling portable trust across marketplaces.

---

## 4. Deal Lifecycle

1. **Create**: Seller defines terms and deadlines; a deal ID and PDA are derived.
2. **Fund**: Buyer deposits USDC into the program vault. The program snapshots USD price.
3. **Deliver**: Seller delivers goods or services.
4. **Release**: Buyer confirms and funds are released to the seller. If not, a dispute can be opened before the deadline.
5. **Dispute**: Parties submit evidence; the AI arbiter returns a signed decision. Optional appeal to human reviewer.
6. **Finalize**: Program executes the decision. Reputation attestations are emitted.

---

## 5. Security and Trust Boundaries

* **Custody integrity**: Funds cannot move without valid state transitions and verified signatures.
* **Keyed arbiters**: ResolveTickets must be signed with registered arbiter keys. Human override keys are managed separately.
* **Immutability**: Evidence and rationale are referenced by cryptographic CIDs.
* **Privacy by design**: Sensitive files may be client-side encrypted prior to upload. AI logs exclude raw PII.

---

## 6. Repository Map

The organization currently includes the following repositories. Public versus private status is indicated for clarity.

* **dev-infra** (Public): Dev containers, docker-compose, localnet bootstrap, environment templates, meta docs.
* **web-app** (Public): End-user UI with wallet adapter. Talks to the actions-server for transaction generation.
* **arbiter-service** (Public): AI pipeline that evaluates evidence and chat and emits a signed ResolveTicket (ed25519, CBOR payload).
* **onchain-escrow** (Public): Escrow program that owns custody and lifecycle state machine on Solana.
* **actions-server** (Public): Solana Actions and Blinks endpoints returning ready-to-sign transactions and enforcing fee-payer policy.
* **core-domain** (Public): Pure TypeScript domain types and business helpers, zero IO.
* **whitepaper** (Public): This document and related technical writeups.
* **storage-lib** (Private): Arweave and IPFS abstraction, hashing, dual pinning, integrity verification.
* **examples** (Private): Runnable end-to-end samples and Postman collections stitching published packages and endpoints.
* **tickets-lib** (Private): ResolveTicket schema (zod), canonical CBOR encode and verify, ed25519 signing and verification.
* **jobs-service** (Private): Deadlines, reminders, auto-escalations. Consumes webhooks and can trigger resolve or notifications.
* **solana-kit** (Private): Thin, typed client for the escrow program and helpers for Actions and transaction building.

---

## 7. Deployment Targets

* **Public testnet availability by December 4, 2025.**
* **Mainnet deployment target by May 2026**, pending audits, infra readiness, and operational runbooks.

These targets reflect intended availability windows, not speculative feature promises.

---

## 8. Interfaces and UX Summary

* **Wallets**: Phantom, Solflare, Backpack via wallet adapter.
* **Link-first**: Shareable Blinks for create, fund, dispute, and release.
* **Evidence UX**: File uploads with CID confirmations and preview before submit.
* **Status clarity**: Deal status badges, timers for dispute windows, and resolution rationale links.

---

## 9. Glossary

* **ResolveTicket**: Signed artifact issued by the AI arbiter or human reviewer with final action and rationale CID.
* **CID**: Content identifier for immutable storage references.
* **PDA**: Program Derived Address controlling program-owned vaults.
* **Actions and Blinks**: Solana link flows that serialize transactions for one-click wallet previews.

---

## 10. References

This writeup consolidates the team’s current architecture and repository layout and aligns wording with the capstone documentation already shared with instructors and reviewers.

Submitted By Bijay Prasai On November 8, 2025.
