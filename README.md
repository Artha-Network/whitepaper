# Artha Network White Paper

## Abstract
Artha Network introduces a decentralized AI-powered escrow protocol designed to make peer-to-peer (P2P) transactions as safe as buying from Amazon. By combining **blockchain smart contracts** with **AI-driven arbitration**, Artha enables secure, low-cost, and scalable escrow services for everyday use — from buying used cars to gig-economy payments.

---

## 1. Problem Statement
P2P commerce is booming, but trust is broken. Fraud in private sales, rentals, freelance gigs, and cross-border deals has reached record highs. Existing escrow solutions are too expensive, too complex, or too centralized. Ordinary people lack an affordable, reliable way to transact with strangers.

---

## 2. Solution Overview
Artha Network solves this problem with a **decentralized escrow protocol** that is:
- **Non-custodial**: Funds locked in smart contracts, never controlled by intermediaries.
- **AI-Assisted**: Automated arbitration for disputes, with human fallback.
- **Accessible**: 1-click onboarding, no crypto knowledge required.
- **Affordable**: Stablecoin-only, gas-sponsored transactions, making even $10 micro-escrows viable.

---

## 3. Why Now?
- **Explosion of P2P markets**: Facebook Marketplace, resale apps, freelance platforms.  
- **Fraud crisis**: Billions lost to scams each year.  
- **Blockchain maturity**: Low-fee L2s and Solana enable cheap, scalable trust.  
- **AI breakthroughs**: LLMs can now triage disputes faster and cheaper than human arbitrators.  

---

## 4. Technical Architecture

### On-Chain Layer
- **Escrow smart contracts** (Rust/Anchor on Solana; Solidity/Foundry for L2).  
- **Stablecoin vaults**: USDC native (stable, predictable value).  
- **State machine enforcement**: Deal lifecycle (initiate → fund → deliver → dispute → resolve → release/refund).  
- **Pyth Oracles**: Real-time USD pricing snapshots.  

### Off-Chain Layer
- **AI Arbiter Service** (Gemini/ChatGPT) → reviews evidence (chats, receipts, images).  
- **Evidence storage**: Arweave + IPFS with CIDs stored on-chain.  
- **API backend**: NestJS/TypeScript, PostgreSQL, Redis queues.  
- **Dispute pipeline**: AI → low-confidence → human override → signed verdict → on-chain resolution.  

### User Experience
- **Actions & Blinks**: Escrow launched from a link or QR code in chats.  
- **Gas sponsorship**: Fee payer model → no SOL/ETH required for first-time users.  
- **Wallets**: Phantom, Solflare, Backpack (plus embedded wallets for non-crypto natives).  

---

## 5. Key Features
- **Micro-escrow**: Transactions as low as $10.  
- **AI arbitration**: Fast, scalable dispute handling.  
- **Human fallback**: Escalations resolved by reviewers.  
- **Reputation layer**: Portable deal history + verified seller badges.  
- **Compliance hooks**: KYC for high-risk corridors, sanctions screening.  

---

## 6. Roadmap

**MVP (8–10 weeks)**  
- USDC escrow on Solana.  
- Actions/Blinks integration.  
- AI Arbiter v0 + human fallback.  
- Pyth snapshot + evidence storage.  

**v1.1**  
- Multi-asset deposits → auto-swap to USDC.  
- Basic reputation scoring.  
- Dialect alerts + Blink links.  

**v2.0**  
- Decentralized arbitration (jury pools).  
- DAO governance of protocol fees.  
- Cross-chain support (Base, Optimism).  
- SDK/API integration with external marketplaces.  

---

## 7. Future Integrations: Real-World Data & Trust
While the MVP focuses on digital escrow, the long-term vision is to **bridge blockchain escrow with real-world verification layers**, creating a holistic trust infrastructure. Planned integrations include:  

- **Government & DMV Records**  
  Escrow release tied to DMV confirmation of title transfer in vehicle transactions.  

- **Blockchain-Based ID Verification**  
  Integration with decentralized identity (DID) frameworks to strengthen fraud prevention.  

- **Property & Rental Registries**  
  Escrow tied to land registries or lease agreements.  

- **Payment Rail APIs & Banking Data**  
  Integrations with Plaid/Finicity for fiat verification when bridging crypto ↔ fiat.  

This ensures escrow is not just secure on-chain but also reflects **actual legal ownership and compliance in the physical world**.

---

## 8. Business Model
- **Transaction Fee**: Flat $1–$2 per escrow (or %-based for large deals).  
- **Enterprise Integration**: SaaS-style licensing for marketplaces and wallets.  
- **Premium Services**: Verified seller badges, faster arbitration.  
- **Future Tokenomics** (optional): Governance token for jury staking + revenue sharing.  

---

## 9. Market Potential
- **P2P sales** (Facebook Marketplace, Craigslist): $200B+ globally.  
- **Freelance/gig economy**: $455B+ global market.  
- **NFTs/digital assets**: $20B+ annually.  
- **Cross-border remittances**: $700B+ yearly.  

Even capturing **0.1% of these markets** represents multi-billion-dollar volume.  

---

## 10. Security & Trust Model

Artha Network's security posture is designed around the principle of **defense in depth** — multiple independent layers that each reduce risk on their own and compound when combined.

### On-Chain Security
- **Non-custodial vaults**: Funds are held in program-derived accounts (PDAs) governed by the smart contract, not by any team wallet or multisig. No single party can move funds outside the state machine.
- **State machine enforcement**: Every transition (fund, dispute, release, refund) is validated on-chain. Invalid state changes are rejected at the instruction level.
- **Anchor framework**: The Solana program uses Anchor's account validation macros, eliminating common Solana vulnerabilities (missing signer checks, account confusion, reinitialization attacks).
- **Deterministic payouts**: Fee calculations use integer arithmetic with basis points (0–10,000) to avoid floating-point rounding errors. Payout logic is implemented as a pure function with no external dependencies.

### Evidence Integrity
- **Content-addressed storage**: Evidence is hashed client-side (SHA-256) before upload. CIDs are stored on-chain, creating an immutable link between the deal and its evidence.
- **Dual-pin strategy**: Evidence is pinned to both IPFS (fast retrieval) and Arweave (permanent storage). Integrity is verified on fetch by recomputing the hash.
- **Tamper detection**: Any modification to stored evidence changes its hash, which will not match the on-chain CID — making post-hoc tampering detectable.

### Dispute Resolution Security
- **Signed verdicts**: AI Arbiter decisions are encoded as canonical CBOR, signed with Ed25519, and verified on-chain before execution. A forged or tampered verdict will fail signature verification.
- **Confidence gating**: Low-confidence AI decisions are automatically escalated to human review rather than executed, preventing unreliable automated outcomes.
- **Replay protection**: Each verdict carries a nonce and expiration timestamp. The on-chain program rejects expired or replayed tickets.

### Operational Security
- **No stored private keys**: The backend never holds user private keys. Transaction signing happens in the user's wallet (browser extension or embedded MPC wallet).
- **Rate limiting and input validation**: All API endpoints validate inputs with Zod schemas, enforce rate limits, and sanitize user-provided strings to prevent injection attacks.
- **Fire-and-forget notifications**: Notification failures never block deal processing. They are wrapped in try/catch and logged for debugging without affecting the critical path.

---

## 11. SWOT Analysis
**Strengths**: Decentralized, low-cost, AI arbitration, micro-escrow support.  
**Weaknesses**: User education, early AI limitations, regulatory friction.  
**Opportunities**: First-mover in micro-escrow, integrations into marketplaces, expansion to rentals/remittances.  
**Threats**: Competitors (PayPal, eBay), regulatory crackdowns, user reluctance.  

---

## 12. Vision
Artha Network aims to become the **trust layer of the global P2P economy**.  
In the future, escrow won’t stop at digital contracts. With external data integrations — from DMV ownership checks to blockchain IDs — Artha will make **real-world transactions auditable, verifiable, and fraud-proof**.

---

*Author: Birochan Mainali — September 2025*
