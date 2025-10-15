# Banx Web3 Payments — 24‑Hour Engineer Test (dApp, **Web3Auth**)

> **Comp:** Fixed **$100 USD** for this 24‑hour test.  
> **Start:** **DM your email** to get a **Slack invite**. Once in Slack, we’ll pay **25% upfront** via PayPal to begin and **75% on completion** (after acceptance of deliverables).  
> **Timer:** Starts when you post in Slack: `I'm starting the 24-hour Banx test now. You have 24 hours to Ship it.`

---

## Objective (single deliverable)
Ship a **live, premium Web3 pre-sale purchase flow** that works **end‑to‑end in real time**:

**Connect/Create Wallet (Web3Auth) → Buy BANX → Live Order Stream + loading screen for anticipation → Congratulations → Book Call (Cal.com) → Telegram / Referral success page.**

Record a **single‑take Loom** (no edits) walking through the **live app** (+ Vercel dashboard). and the database.

---

## Product spec (what to build)

### Token & sale mechanics
- **Token:** **BANX** (ERC‑20).  
- **Network:** **Sepolia** (Ethereum testnet).  
- **Sale model (MVP):** a simple **TokenSale** contract that accepts testnet **ETH** and transfers **BANX** to the buyer at a **fixed rate** (e.g., `TOKENS_PER_ETH`). The contract **emits**  
  `Purchase(address buyer, uint256 ethAmount, uint256 tokenAmount, uint256 timestamp)` on every buy.  
- **Inventory:** Pre‑mint the supply to the sale contract at deploy (or mint to treasury then transfer). **Disable minting** thereafter.

### dApp user flow (must match UX)
- **Landing (Next.js):** premium look (**black/ivory with subtle gold accents**), short pitch, **Connect or Create Wallet (Web3Auth)**.  
  - **Requirement:** After connect/create, **persist wallet to DB** immediately (with minimal profile data if available).  
- **Purchase widget:**  
  - **Inputs:** desired **USD** amount (simulate USD via static rate, e.g., `NEXT_PUBLIC_ETH_USD=3000`). Show calculated **ETH** and **BANX** to receive.  
  - **Action:** On **Buy**, send a payable tx to `TokenSale.buy()`. Display **TX hash**, **pending/success** states, and **clear error** states.  
- **Live order stream (split pane):** Right side shows a **real‑time feed** of `Purchase` events (buyer • ETH • BANX • time). **Subscribe via WebSocket RPC; no polling.** The feed **must update during the Loom** while you perform a buy.  
- **Congratulations page:**  
  - Copy: **“You purchased $XX (≈ YY BANX)”**  
  - CTA: **“Book a 10‑min call”** to see a demo & secure **discounted BANX** bundles.  
- **Book a Call (Cal.com):** redirect with **prefilled query params** (`name`, `email`, `wallet`) gathered via Web3Auth user info (or a light inline form if not available).  
  - After booking, show copy: **“You’re booked with one of our advisors to learn more inside info before we list.”**
  - Offer buttons: **“Join Telegram Community”** and **“Refer a Friend”**.  
- **Success → Telegram:** Button opens `NEXT_PUBLIC_TELEGRAM_URL`. Add a secondary **“Copy referral link”** (dummy is fine).  
- **Inspiration only (do not clone):** https://blockdag.network/ and https://blockchainfx.com/ — tasteful, not spammy.

---

## Tech requirements (keep it simple)

### Frontend
- **Next.js 15** (App Router, **TypeScript**).  
- **Web3Auth** for wallet **connect/create** (email/social → wallet).  
- **wagmi + viem** for on‑chain interactions (MetaMask/WalletConnect optional).  
- **Tailwind** + minimal components (**ivory/black/gold** premium fintech feel).  
- **Real‑time events:** client subscribes to `Purchase` via **WS** (Alchemy/Infura Sepolia).

### Smart contracts
- **Solidity** with **Hardhat** (or Foundry).  
- **Contracts:**  
  - **BanxToken (ERC‑20):** name **Banx Token**, symbol **BANX**, 18 decimals; supply minted to **TokenSale** (or transfer on deploy); **minting disabled** afterward.  
  - **TokenSale:** configurable price (tokens/ETH); `buy()` payable → transfers tokens; **emits `Purchase(...)`**; **guardrails:** cannot oversell; reject zero‑value.  
- **Deployment:** scripts to deploy to **Sepolia** (+ optional Etherscan verification).

### Backend
- **Required (minimal):** an API to **persist wallets** (and optional profile fields) to a DB **on the landing connect/create step**.  
  - DB can be **Postgres** or **SQLite**. Store: `user_id (if any)`, `wallet_address`, `email (if available)`, `created_at`.  
  - Provide a simple **/admin/wallets** JSON endpoint for verification in Loom.
- **Optional:** log each purchase row.

### Scheduling
- **Cal.com** booking link with **prefilled** name/email/wallet via query string.

### Deployment (Vercel‑first)
- **Deploy to Vercel** (primary). If you add an API, host it in the same Next.js app (App Router routes).  
- Use **Vercel Environment Variables** for all secrets (RPC, Web3Auth Client ID, Cal URL).  
- **GitHub flow:** fork or create a **feature branch**; open a PR; enable **Preview Deployments** so we can review your link history.

### Environment variables (example)
```env
# Frontend
NEXT_PUBLIC_NETWORK=sepolia
NEXT_PUBLIC_SALE_ADDRESS=0x...         # after deploy
NEXT_PUBLIC_TOKEN_ADDRESS=0x...        # after deploy
NEXT_PUBLIC_WS_RPC_URL=wss://...       # Alchemy/Infura WS (Sepolia)
NEXT_PUBLIC_ETH_USD=3000
NEXT_PUBLIC_TELEGRAM_URL=https://t.me/banx_test_group
NEXT_PUBLIC_CAL_URL=https://cal.com/banx/demo

# Web3Auth
WEB3AUTH_CLIENT_ID=...                 # required for connect/create

# DB / API (if using Postgres; SQLite ok locally)
DATABASE_URL=postgres://...
```

---

## What to ship (we will check only these)
- **Live app URL** (Vercel).  
- **Contract addresses** (Token + Sale) on **Sepolia**.  
- **GitHub repo** (public or invite us) with a **feature branch** used for your work.  
- **Single‑take Loom (≤8 min)** showing:  
  - Live dApp: **connect/create wallet → USD entry → buy → tx hash → real‑time order feed** updates without refresh.  
  - **Congrats** page with USD + BANX amounts.  
  - **DB proof:** show `/admin/wallets` (or DB table) containing your wallet from the **landing step**.  
  - **Cal.com** booking prefilled → post‑booking message → **Telegram** link opens.  
  - **Vercel**: show the project + an active **Preview Deployment**.

> No screenshots. No edits. One clean pass.

---

## Acceptance criteria (pass/fail)
- **Web3Auth** connect/create works; **wallet captured to DB on landing**.  
- Contracts deployed on **Sepolia**; visible on a block explorer.  
- Purchase widget works with **pending/success/error** states and **tx hash**.  
- **Live order stream** updates within seconds via **WS** (no polling).  
- **Congrats** shows **USD + BANX**.  
- **Cal.com** opens with **prefilled** info; post‑booking copy appears; **Telegram** button works.  
- **Vercel** deployment with env vars (no secrets in repo).  
- **README** is clear; env vars & deploy steps documented.

---

## Scoring rubric (100)
- **Core flow (45):** Web3Auth + wallet DB capture (15), purchase + states (15), real‑time feed (15)  
- **UX polish (15):** premium styling, clean copy, mobile tolerable  
- **Infra/DX (15):** Vercel deploy, env vars, sensible structure, feature branch/PR  
- **Code quality (15):** types, readability, minimal tests (contract unit tests)  
- **Speed & clarity (10):** shipped in **≤24 hours**; README is crisp  
- **Bonus (+10):** per‑wallet cap; E2E test (Playwright); WS relay; mini event indexer

---

## Local dev quickstart (example)
```bash
# Contracts
cd contracts
cp .env.example .env           # add Sepolia RPC + PRIVATE_KEY
npm i
npx hardhat compile
npx hardhat run scripts/deploy.ts --network sepolia
# capture TOKEN + SALE addresses

# App (Next.js)
cd ../app
cp .env.example .env.local     # fill vars from above
npm i
npm run dev
```

---

## System diagram (Mermaid)

```mermaid
flowchart LR
  subgraph User["User (desktop/mobile)"]
    L[Landing / Connect]
    P[Purchase]
    G[Congrats]
    B[Book Call]
    S[Success / Telegram]
  end

  subgraph FE["Next.js (Vercel)"]
    FE_API[App Router API routes]
    FE_WS[WS Subscriptions]
  end

  subgraph Web3["Web3Auth"]
    WAuth[Connect / Create Wallet]
  end

  subgraph Chain["Sepolia (Ethereum Testnet)"]
    T[BanxToken (ERC-20)]
    SLC[TokenSale Contract]
  end

  subgraph DB["DB (Postgres/SQLite)"]
    WAL[wallets table]
    ORD[orders table (optional)]
  end

  subgraph Ext["External Services"]
    CAL[Cal.com]
    TG[Telegram Group]
    ETHSCAN[Etherscan]
    WSProv[Alchemy/Infura WS]
  end

  %% User flow
  L -->|Connect/Create| WAuth --> FE
  FE -->|Persist wallet| WAL
  L --> P
  P -->|Buy| FE_WS -->|send tx| SLC
  SLC -->|emit Purchase| WSProv --> FE_WS
  FE_WS -->|append row| P
  SLC -->|transfer tokens| T
  SLC -->|tx hash| ETHSCAN
  P --> G
  G --> B --> CAL --> S
  S --> TG

  %% Optional order logging
  FE_API --> ORD
```

---

## Notes & gotchas
- **Web3Auth user info:** if name/email aren’t available, add a tiny inline form before Cal.com to collect them for prefill.  
- **USD display** is informational (static env var); ETH only on Sepolia.  
- Keep the widget **fast & frictionless**; if anything is slow/confusing, cut scope and polish the core.

---

## Submit (in one Slack message)
- Live URL (Vercel)  
- Repo link (feature branch/PR)  
- Contract addresses  
- **One unedited Loom (≤8 min)** covering everything above

> Ship this cleanly in **24 hours** and we’ll discuss the full‑time Dubai role (onsite, 40–60 hrs/wk).
