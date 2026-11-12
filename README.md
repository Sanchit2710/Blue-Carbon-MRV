# BlueCarbon — verifiable blue-carbon credits

**BlueCarbon is a monitoring, reporting and verification (MRV) platform for coastal restoration.** NGOs and panchayats register mangrove, seagrass and salt-marsh restoration sites and send field evidence; independent verifiers check it; approved results become carbon credits on the Ethereum blockchain, where companies can buy them and retire them for an offset claim — with every step recorded so anyone can check it.

## What the words mean

- **Blue carbon** — carbon captured and stored by coastal and marine ecosystems such as mangroves, seagrass meadows and salt marshes. Restoring them removes CO₂ from the air and protects coastlines.
- **MRV (Monitoring, Reporting, Verification)** — the process that turns restoration work into trustworthy numbers: *monitor* the site, *report* the evidence, and have an independent party *verify* it before any credit exists.
- **Carbon credit** — a record that one tonne of CO₂-equivalent (1 tCO₂e) was removed or avoided, when the crediting methodology allows it. Companies buy credits and **retire** them to back a climate claim; a retired credit must never be used again.

## The problem

Restoration records usually live in private databases. Outsiders can't easily tell whether a credit was issued only once, who owns it now, or whether it has already been used. Evidence photos can be swapped or reused, and long-term site maintenance is hard to track.

## How BlueCarbon solves it

1. **Register** — an NGO registers a site: boundary, area, species, planting date, ten-year stewardship commitment.
2. **Evidence** — field photos are uploaded at full original quality. Each gets a SHA-256 fingerprint and its GPS and capture time are checked against the registered boundary; duplicates, look-alike photos and low-resolution copies are flagged for the verifier.
3. **Verify** — an independent verifier approves, rejects or asks for more evidence. The decision and the reason are recorded, and reviewers can't review their own organisation.
4. **Issue credits on-chain** — the verifier signs with their own MetaMask wallet; the smart contract mints the credit batch to the NGO's wallet. The same project and period can never be issued twice.
5. **Sell** — the NGO lists credits at a price. A company buys them, and the credits and the payment swap in **one** transaction, so a listing can never sell more than the NGO actually holds.
6. **Retire** — the company retires credits; they are burned and can never move again. A public, printable retirement certificate links to the transaction.
7. **Monitor** — approval opens a ten-year schedule of annual monitoring reports; missed or failed reports are flagged.

**What goes where:** ownership, issuance, sales and retirement are on the blockchain (public and tamper-evident). Photos, decisions and reports stay private in the database, protected by row-level security, with their fingerprints kept for later checks. The server re-reads every blockchain receipt before it records anything, so the database never takes the browser's word for it.

> A blockchain proves that records weren't changed and who did what. It does not prove that a mangrove is alive — that is what field evidence and verifiers are for. Tokens here live on a test network and are not legally recognised carbon credits; recognition depends on the crediting programme.

## Live on Sepolia (Ethereum test network)

| Contract | Address | Purpose |
|---|---|---|
| BlueCarbonRegistry | [`0x9941E54386dA5bBb389Efdf482C175F0a620b031`](https://sepolia.etherscan.io/address/0x9941E54386dA5bBb389Efdf482C175F0a620b031) | Projects, ERC-1155 credit batches, retirement, monitoring milestones; only verifiers can issue |
| BlueCarbonMarketplace | [`0xa0Ff80D20FF18AdF73ee510920656563D22abefA`](https://sepolia.etherscan.io/address/0xa0Ff80D20FF18AdF73ee510920656563D22abefA) | Fixed-price listings with atomic, oversell-proof settlement |

A complete issue → list → buy → retire cycle has been run on Sepolia with three separate wallets (verifier, NGO, company).

## Features

- **Three roles** — NGO / panchayat, company, verifier — each with its own dashboard; access enforced in the database, not just the UI.
- **Evidence pipeline** — full-quality originals, SHA-256, EXIF and phone GPS, boundary check, duplicate / similarity / resolution flags, private storage, download original.
- **Verification workflow** — review queue, decisions with reasons, conflict-of-interest rule, audit log, ten-year monitoring with missed-report alerts.
- **Wallets** — organisations link a MetaMask wallet by signing a message; one wallet per organisation.
- **Marketplace** — public listings with ecosystem, region and price filters; public project pages with an OpenStreetMap site map, coordinates and a drawn-vs-declared area check.
- **Retirement certificates** — public, printable, linked to the on-chain transaction.
- **Demo mode** — clearly labelled demonstration data to show the whole flow without a wallet.

## Tech stack

| Layer | Technology |
|---|---|
| Frontend | Next.js 16, React 19, TypeScript, Tailwind CSS 4, shadcn/ui, Motion, Leaflet + OpenStreetMap |
| Backend & database | Supabase — PostgreSQL with row-level security, Auth, private Storage |
| Smart contracts | Solidity 0.8.24, OpenZeppelin 5 (ERC-1155, AccessControl, Pausable, ReentrancyGuard), Hardhat |
| Blockchain | Ethereum Sepolia testnet (chain ID 11155111) via Alchemy |
| Web3 | ethers.js v6, MetaMask |

## Repository structure

```
apps/web/        Next.js app (dashboards, marketplace, evidence pipeline, wallet flows)
contracts/       Solidity contracts, Hardhat tests, deploy scripts, Sepolia deployment record
supabase/        Database migrations 0001–0008 and SQL security tests
docs/            Architecture, credit lifecycle, contracts, setup and live-demo guides
```

## Run it locally

```bash
cd apps/web
cp .env.example .env.local    # Supabase URL/keys, Sepolia RPC URL, contract addresses
npm install
npm run dev                   # http://localhost:3000
```

1. Create a Supabase project and run `supabase/migrations/*.sql` in order — see [`docs/supabase-setup.md`](docs/supabase-setup.md).
2. Optional demo data: `npm run seed:demo` (remove with `npm run seed:demo:reset`).
3. Live blockchain demo with MetaMask: [`docs/live-demo.md`](docs/live-demo.md).

## Tests

```bash
cd contracts && npx hardhat test    # 26 passing: registry + marketplace
cd apps/web && npm test             # geofence, hashing, area, on-chain ID derivation
```

The SQL suites in `supabase/tests/` check the row-level security rules (who can read and write what).

## Limitations and next steps

- Test network only; single admin key (a multisig is the next step), no external audit.
- Satellite (NDVI) analysis, offline field capture and on-map boundary drawing are planned, not built.

## Team

Team CarbCoders — Smart India Hackathon 2025.

Licensed under the MIT License.
