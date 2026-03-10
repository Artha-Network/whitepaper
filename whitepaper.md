Here's the iOS app section, written to match the exact tone and depth of your existing whitepaper:

---

## 3.4 iOS Application

The iOS application is the primary consumer-facing interface for Artha Network. While the protocol is chain-agnostic and accessible via web, the native iOS app targets the mainstream P2P seller — someone who lists on Facebook Marketplace or Craigslist, has no crypto background, and needs escrow to feel as simple as Venmo.

### Design Philosophy

The app follows a **link-first, wallet-second** model. A buyer or seller initiates a deal from a chat or SMS by sharing a Blink link. The recipient taps it, reviews the deal terms, and funds the escrow — all without understanding what Solana is. Wallet creation and gas sponsorship happen silently in the background on first launch.

Three principles govern every screen decision:

- **No crypto jargon in user-facing copy.** "Funds held securely" not "USDC locked in program-owned vault." The technical reality is unchanged; the language is not.
- **One primary action per screen.** Escrow flows are inherently multi-step. The app never presents two competing CTAs at the same time.
- **Failure is recoverable.** Network errors, wallet creation failures, and disputed evidence uploads surface with a clear next step, never a dead end.

### Core Screens and Flows

**Onboarding**
First-time launch creates an embedded MPC wallet silently using the Turnkey or Privy SDK. Users authenticate with Face ID or Apple Sign-In. No seed phrase is shown or required unless the user explicitly requests key export. SOL for gas is sponsored via a fee-payer backend so the user's first transaction requires zero funding.

**Deal Creation**
A seller fills in: item description, agreed price in USD, delivery method, and optional deadline. The app generates a Blink URL and a QR code. Both can be shared directly into iMessage, WhatsApp, or any chat. The on-chain deal is not created until the buyer funds — no wasted transactions.

**Funding and Confirmation**
The buyer opens the Blink link, reviews deal terms, and approves funding with Face ID. The app constructs and submits the `initiate_escrow` transaction, sponsored by the fee-payer service. A push notification confirms when the vault is funded and visible on-chain.

**Evidence Upload**
Either party can upload evidence at any point: photos from the camera roll, documents from Files, or short video clips. Uploads are hashed client-side before transmission, dual-pinned to IPFS and Arweave via `storage-lib`, and the resulting CIDs are written on-chain. The UI shows upload status per file and flags any integrity check failures before submission.

**Dispute Flow**
Either party can open a dispute during the `DELIVERED` window. The app prompts for a structured description (what went wrong, what resolution is expected) and pulls in already-uploaded evidence. The AI Arbiter result is surfaced as a plain-language summary with a confidence indicator. If confidence is below threshold, the UI shows an appeal option with a countdown timer for the human override window.

**Notifications and Deadlines**
APNs push notifications cover: deal funded, delivery confirmed, dispute opened, arbiter decision issued, and deadline warnings. A background task monitors on-chain state via the NestJS backend and fires notification jobs through the Redis queue. Users can configure notification preferences per deal category.

### Technical Stack

| Layer | Choice | Rationale |
|---|---|---|
| Language | Swift 5.9, SwiftUI | Native performance, declarative UI, full iOS ecosystem access |
| Wallet SDK | Turnkey / Privy embedded MPC | No seed phrase UX, Face ID auth, recoverable via cloud backup |
| Solana RPC | `solana-swift` + custom RPC client | Transaction construction, signing, submission |
| Networking | `URLSession` + async/await | Native, no Alamofire dependency overhead |
| Evidence upload | REST calls to NestJS backend → `storage-lib` | Client never talks to Arweave/IPFS directly |
| Local state | SwiftData (iOS 17+) | Caches deal state, evidence CIDs, notification preferences |
| Push | APNs via NestJS notification job | Triggered by on-chain state change listeners |
| Deep linking | Universal Links + custom `artha://` scheme | Blink URLs open directly into the correct deal screen |

### Wallet Architecture

The app uses an **embedded MPC wallet** rather than a WalletConnect integration. The decision is deliberate: requiring Phantom or Solflare creates a hard dependency on the user already being in crypto. MPC wallets (Turnkey, Privy) generate keys in secure enclaves, authenticate via biometrics, and are recoverable through the user's iCloud account or email — matching the mental model of Apple Pay, not MetaMask.

For power users who want to bring their own wallet (Phantom, Backpack), WalletConnect v2 is supported as a secondary option. It is not the default path.

### Offline and Error Resilience

P2P deals happen in parking lots, driveways, and low-signal areas. The app handles this by:

- Caching deal state locally in SwiftData so terms and evidence are readable offline.
- Queuing evidence uploads and retrying on reconnection — uploads are idempotent since CIDs are content-addressed.
- Showing the last known on-chain state with a staleness indicator when RPC calls fail.
- Never blocking the evidence submission UI on network availability. Files are queued locally and flushed when connectivity returns.

### Security Considerations

- Private keys never leave the secure enclave. Signing happens on-device.
- Evidence files are hashed client-side before upload. The backend rejects any payload whose hash does not match the declared CID.
- All API calls use short-lived JWTs issued at app launch. Tokens are stored in the iOS Keychain, not `UserDefaults`.
- Jailbreak detection runs at launch. The app does not block on a jailbroken device but flags it in the session metadata sent to the backend.

### App Store and Distribution

The MVP targets iOS 17+ to use SwiftData and the latest SwiftUI APIs without polyfill overhead. TestFlight distribution covers internal testing and a closed beta with selected marketplace communities. App Store submission targets the Finance category with a clear escrow use-case description. Given the stablecoin and crypto wallet components, the app will include the required financial services disclosure and comply with Apple's guidelines on cryptocurrency wallets (App Store Review Guideline 3.1.5(b)).

---

Paste that directly after your `### 3.3 Evidence, resolution, reputation` block as `### 3.4 iOS Application`. It follows the same heading depth (`###`) and matches your existing table/bullet formatting style throughout.
