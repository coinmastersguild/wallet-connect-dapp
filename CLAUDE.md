# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Overview

This is a WalletConnect v2 wallet implementation example that demonstrates connecting a KeepKey hardware wallet to WalletConnect dApps. The wallet implements Sign v1 and v2 side-by-side for backwards compatibility.

## Development Commands

```bash
# Install dependencies
yarn install

# Run development server (port 3001)
yarn dev

# Build for production
yarn build

# Start production server
yarn start

# Run linting
yarn lint

# Format code
yarn prettier:write
```

## Environment Setup

1. Obtain a WalletConnect Project ID from [WalletConnect Cloud](https://cloud.walletconnect.com/sign-in)
2. Set up environment variables:
```bash
cp .env.local.example .env.local
```

Required environment variables:
- `NEXT_PUBLIC_PROJECT_ID` - WalletConnect Project ID
- `NEXT_PUBLIC_RELAY_URL` - Default: `wss://relay.walletconnect.com`
- `NEXT_PUBLIC_COVALENT_API_KEY` - For blockchain data
- `NEXT_PUBLIC_ETHPLORER_API_KEY` - For Ethereum token data
- `NEXT_PUBLIC_BLOCKCHAIR_API_KEY` - For UTXO chains

## High-Level Architecture

### Core Flow
1. **Initialization** (`src/pages/_app.tsx`):
   - Initializes KeepKey wallet connection via `useKeepKey` hook
   - Creates Web3Wallet instance for WalletConnect
   - Sets up event managers for handling WalletConnect requests

2. **KeepKey Integration** (`src/hooks/useKeepKey.ts`):
   - Connects to KeepKey device running on `http://localhost:1646`
   - Uses `@coinmasters/wallet-keepkey` SDK
   - Currently configured for ETH chain only (easily extendable to multi-chain)
   - Stores API key in localStorage for session persistence

3. **WalletConnect Events** (`src/hooks/useWalletConnectEventsManager.ts`):
   - Subscribes to session proposals, requests, and other WalletConnect events
   - Opens appropriate modal views for user approval/rejection
   - Routes approved requests to appropriate handlers

4. **Request Handling** (`src/utils/EIP155RequestHandlerUtil.ts`):
   - Processes EIP155 signing methods (personal_sign, eth_sign, typed data, transactions)
   - Uses KeepKey wallet instance from `eip155Wallets` to perform cryptographic operations
   - Returns formatted JSON-RPC responses

### Key Components

- **Modal System**: Located in `src/views/`, handles user interactions for session proposals, sign requests, etc.
- **Chain Data**: Configurations for multiple chains in `src/data/` (EIP155, Cosmos, Solana, etc.)
- **Store Management**: Uses Valtio for state management (`src/store/`)

### KeepKey-Specific Implementation

The wallet replaces the default in-browser wallet with KeepKey hardware wallet:
- `EIP155WalletUtil.ts` is modified to accept KeepKey instance
- All signing operations delegate to KeepKey device
- Balance and address fetching uses KeepKey SDK methods

### Testing Integration

DOM elements are tagged with `data-testid` attributes for E2E testing. Key identifiers include:
- Navigation: `accounts`, `sessions`, `wc-connect`, `pairings`, `settings`
- Session management: `session-card`, `session-approve-button`, `session-reject-button`
- Request handling: `request-button-approve`, `request-button-reject`

## Important Notes

- The KeepKey device must be running locally on port 1646
- Legacy v1 code files are prefixed with `Legacy...`
- This is a development example - not intended for production use with real funds
- Currently configured for ETH only but supports multi-chain architecture