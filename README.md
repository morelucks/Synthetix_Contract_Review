# Synthetix_Contract_Review

| Clint        | Synthetix  |
|-------------------|-------------|
| Title	             | Sythetix contract review    | 
| Target	            | Sythetix.sol     |    
|  Author       | Kamshak Lucky Isuwa  |
|Classification | Pubic|
|status       | Draft  |
|Date created  |  18th November, 2024|

# Synthetix Protocol Overview

Synthetix is a liquidity provisioning protocol that enables the creation and trading of synthetic assets (synths) backed by staked Synthetix Network Tokens (SNX). These synthetic assets provide users with exposure to various real-world assets without holding the actual underlying asset.

---

## Core Functionality

### Synthetic Assets (Synths)
- Synths are tokenized representations of real-world assets such as:
  - Fiat currencies
  - Commodities
  - Stocks
  - Cryptocurrencies
- Synths allow users to trade and gain exposure to various assets seamlessly.

### How It Works
1. **Collateralization with SNX**  
   - SNX tokens are staked in a smart contract as collateral.  
   - Locked SNX enables the issuance of synthetic assets.  

2. **Pooled Collateral Model**  
   - Synths are supported by a pooled collateral model, enabling direct conversions between different synths via smart contracts.  
   - This eliminates the need for counterparties in trades.  
