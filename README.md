# Solace — Your Personal AI Companion (Web3-first)

Solace is a privacy-first AI companion web app that combines an on-device React frontend, Google Gemini for conversational AI, and decentralized storage for chat history. It uses Web3 wallet onboarding and a simple subscription model on the Polygon Amoy testnet to gate access.

This README gives a high-level overview, quick start instructions, architecture notes, and guidance for production hardening.

## Key features

- 24/7 AI companion powered by Google Gemini (streaming responses)
- Wallet-based onboarding using MetaMask / injected Ethereum provider
- Trial & subscription access control stored locally
- Optional persistence of chat history to decentralized storage (Lighthouse / IPFS)
- Lightweight React + Tailwind UI focused on privacy and ownership

## Quick demo / screenshot

The app ships with a clean landing page, a guided onboarding flow, wallet connect flow, and a dashboard where you interact with your AI companion. See the repository for UI components.

## Local development

Prerequisites
- Node.js 18+ (recommended)
- A Web3 wallet (MetaMask) for testing wallet flows
- Optional: an account and API key at Lighthouse.storage if you want to test chat uploads

Install
1. Clone the repo
   git clone https://github.com/benaiah-muga/Solace.git
2. Install dependencies
   npm install

Run
- Start the dev server:
  npm run dev

Open http://localhost:5173 (or the address printed by your dev server).

Build
- Build the production bundle:
  npm run build

Serve a production build locally:
- Use a static server to serve the `dist/` folder (e.g. `npm install -g serve && serve dist`)

## Configuration & Secrets

Important: the repository contains placeholder keys for development and demo convenience. Do NOT commit real API keys in a public repo.

Files and keys to configure for production:
- Google Gemini API key
  - File: `services/geminiService.ts` (development placeholder)
  - Env var suggestion (production): provide via a secure server-side proxy or an environment variable on a serverless function that the frontend calls.
- Lighthouse (web3 storage) API key
  - File: `services/web3storageService.ts` (development placeholder)
  - Create a key at https://files.lighthouse.storage/ (Wallet -> API Key), then store it server-side or in a secure environment variable.

Other constants:
- `constants.ts` contains Polygon Amoy chain id, RPC URL and subscription/pricing constants. Adjust for production chain and addresses as needed.

Why you should not keep keys in the client
- Browsers and client-side bundles are public; any embedded key can be scraped and misused.
- For production, implement a backend endpoint that proxies requests to external APIs with authentication and rate limiting.

## Architecture & Data flows

- Frontend: React + Tailwind — handles UI, wallet connection, onboarding, and streaming UI for AI responses.
- AI: Google Gemini (via @google/genai library). The app uses streaming responses to show the assistant in real-time.
- Storage: Lighthouse (IPFS) to persist chat transcripts, returning a CID that can be used to access the file from an IPFS gateway.
- Wallet gating: The app uses an injected Ethereum provider to request accounts and manage a simple trial/subscription mechanism stored in localStorage.

Security & privacy notes
- Chat transcripts are uploaded to a third-party decentralized storage (Lighthouse/IPFS) only if you choose to. The CID is public on IPFS if shared.
- Do not store secrets in client code. Move third-party credentials and the Gemini key to a secure backend.
- Consider encrypting chat history prior to upload for additional privacy guarantees.

## Recommended production changes / TODOs

- Move all API keys and AI calls to a backend service or serverless functions and keep only minimal tokens/IDs on the client.
- Implement an authenticated subscription backend (Stripe, subscription contract, or on-chain payment verification) rather than relying solely on localStorage.
- Add robust error handling, monitoring, logging, and rate limiting on backend AI proxy endpoints.
- Add tests (unit & E2E) for onboarding, wallet flows, and streaming behavior.
- Harden content moderation and abuse prevention for the AI assistant (safety layers, filtered prompts, and content policies).
- Use environment-specific constants and secure secret management (e.g., GitHub Secrets, Vercel/Netlify env vars, or a cloud secret store).

## Troubleshooting & common issues

- Blank screen on load:
  - The app previously embedded API keys directly to avoid a blank-screen crash (process.env isn't available in some browser importmap setups). If you see a blank screen, check the browser console for missing imports or runtime errors.
- Wallet not found:
  - Ensure a Web3 provider (like MetaMask) is installed and enabled for the site.
- Wrong network:
  - The app attempts to switch to Polygon Amoy (testnet). If your wallet doesn't have the chain, the app will attempt to add it. You can also add it manually with the RPC details in `constants.ts`.

## Contribution

Contributions are welcome! A few guidelines:
- Open an issue to discuss larger features or design changes.
- Keep PRs small and focused.
- Add relevant tests and update documentation when adding features.
- Use feature branches and describe your changes in the PR.

## License

This project is provided as-is for demonstration and learning purposes. Add an appropriate license file (e.g., MIT) if you wish to open-source it for reuse.

---

If you'd like, I can:
- Push this README.md into the repository for you, or
- Create a smaller version focused on developer setup, or
- Draft a CONTRIBUTING.md and SECURITY.md with hardening recommendations.

Tell me which option you'd like next and I will apply it.
