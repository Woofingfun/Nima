# NimaToken Smart Contract

`NimaToken` is an ERC20-compliant smart contract deployed on the BNB Chain, featuring a unique "Catch" game mechanism that allows users to capture tokens from other addresses under specific conditions. This README outlines the contract's rules, features, and setup instructions.

- **Contract Address**: [0x146b2AE41b06CF83C1a2f0b0a3883DaB370c378e](https://bscscan.com/address/0x146b2AE41b06CF83C1a2f0b0a3883DaB370c378e)
- **Network**: BNB Chain
- **Verification Date**: August 10, 2025
- **Solidity Version**: ^0.8.24
- **License**: MIT

## Token Overview

- **Name**: Nima
- **Symbol**: NIMA
- **Decimals**: 18
- **Maximum Supply**: 1,000,000,000,000,000 (1 quadrillion) NIMA
- **Initial Supply**: 0 (tokens issued via minting)
- **Type**: ERC20 token with a "Catch" game mechanism

## Key Features and Rules

### 1. Token Minting
- **Mechanism**:
  - Each address can call `mint()` once to receive `1,000,000,000,000` NIMA (1/1000th of max supply).
  - Maximum mints: 1,000, limited by the total supply cap.
  - Minted tokens are credited to the caller's balance, triggering `Minted` and `Transfer` events.
- **Restrictions**:
  - An address cannot mint again after its first mint.
  - Minting stops when 1,000 mints are reached or the total supply hits the maximum.

### 2. Catch Game
- **Overview**: Users can "catch" tokens from another address by paying `1 NIMA` (`CATCH_COST`), extracting a portion of the target's balance and burning part of it.
- **Trigger**: 
  - Initiated by calling `transfer(to, amount)` with `amount = 1 NIMA` while the game is active (`isGameActive == true`).
- **Rules**:
  - **Conditions**:
    - Cooldown: Catchers must wait 72,000 seconds (~20 hours) between catches.
    - Target must not be whitelisted.
    - Target's balance must be at least 1 NIMA.
  - **Effects**:
    - 1% of the target's balance is extracted as a reward.
    - Reward split: 70% to the catcher, 30% burned (reducing total supply).
    - Catch details (catcher address, amount, timestamp) are stored in `catchRecords`.
    - Triggers `Caught` and `Burned` events.
- **Cooldown**: 72,000 seconds per address between catches.

### 3. Whitelist
- **Purpose**: Whitelisted addresses are immune to the catch mechanism.
- **Management**:
  - Only the contract owner can add (`addToWhitelist`) or remove (`removeFromWhitelist`) addresses.
  - Triggers `WhitelistUpdated` event.

### 4. ERC20 Standard Functions
- **Transfer**:
  - `transfer(to, amount)`: Transfers tokens; if `amount = 1 NIMA` and the game is active, it triggers a catch.
  - Requirements: Non-zero addresses, sufficient balance.
  - Triggers `Transfer` event.
- **Allowance**:
  - `approve(spender, amount)`: Authorizes another address to spend tokens.
  - Supports `transferFrom`, `increaseAllowance`, and `decreaseAllowance`.
  - Triggers `Approval` event.
- **Queries**:
  - `name()`: Returns "Nima".
  - `symbol()`: Returns "NIMA".
  - `decimals()`: Returns 18.
  - `totalSupply()`: Returns current total supply.
  - `balanceOf(address)`: Returns an address’s balance.
  - `allowance(owner, spender)`: Returns the allowance granted by `owner` to `spender`.

### 5. Catch Records
- **Functions**:
  - `getCatchRecords(target, page, pageSize)`: Retrieves paginated catch records for a target address (catcher, amount, timestamp).
  - `getCatchRecordsCount(target)`: Returns the total number of catch records for a target address.

### 6. Contract Management
- **Owner Privileges**:
  - The deployer is the initial `owner`, with the ability to:
    - Add/remove whitelist addresses.
    - Pause (`stopGame`) or resume (`startGame`) the catch game.
    - Renounce ownership (`renounceOwnership`), setting the owner to the zero address.
  - After renouncing ownership, management functions become unavailable.
- **Events**:
  - `GameStopped`: Emitted when the game is paused.
  - `GameStarted`: Emitted when the game is resumed.
  - `OwnershipRenounced`: Emitted when ownership is renounced.

### 7. Events
- `Transfer`: Emitted on token transfers (including minting, catching, burning).
- `Approval`: Emitted on allowance changes.
- `Minted`: Emitted on token minting.
- `Caught`: Emitted on successful catch.
- `Burned`: Emitted when tokens are burned.
- `WhitelistUpdated`: Emitted on whitelist changes.
- `GameStopped`/`GameStarted`: Emitted on game state changes.
- `OwnershipRenounced`: Emitted when ownership is renounced.

## Security and Restrictions
- **Zero Address Checks**: Transfers and approvals to/from the zero address are prohibited.
- **Balance/Allowance Checks**: Ensures sufficient balance for transfers/catches and sufficient allowance for `transferFrom`.
- **Cooldown**: Prevents frequent catches with a 72,000-second cooldown.
- **Whitelist Protection**: Whitelisted addresses are immune to catches.
- **Irreversible Actions**: Renouncing ownership permanently disables management functions.

## Use Cases
- **Token Economy**: Mint and transfer NIMA tokens or use allowances for delegated spending.
- **Catch Game**: Strategically capture tokens from other addresses to earn rewards, with burns potentially increasing token scarcity.
- **Whitelist Strategy**: Protect key addresses (e.g., team or partners) from catches.

## Setup and Deployment
### Prerequisites
- **Tools**: Node.js, Hardhat/Truffle, or Remix for compilation and deployment.
- **Wallet**: A wallet with BNB for gas fees (e.g., MetaMask).
- **BNB Chain**: Configure your development environment for the BNB Chain (mainnet or testnet).

### Deployment Steps
1. **Clone the Repository**:
   ```bash
   git clone <your-repository-url>
   cd NimaToken
