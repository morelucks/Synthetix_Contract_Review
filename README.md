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


## Key Areas of Synthetix  

### 1. The Synthetix Protocol  
- A **suite of smart contracts** designed to provision liquidity.  
- Works alongside Synthetix Governance to enable financial derivative products.  

### 2. The Synthetix Staking Interface  
- A **web interface** that simplifies interaction with:  
  - Synthetix Staking.  
  - Other Synthetix ecosystem products.  

### 3. Synthetix Governance  
- A **representative council system** that oversees the protocol.  
- Governed by four staker-elected councils:  
  1. **Spartan Council**: Oversees protocol decisions.  
  2. **Treasury Council**: Manages protocol funds.  
  3. **Ambassador Council**: Handles community relationships.  
  4. **Grants Council**: Supports ecosystem development.  

### 4. Synthetix Partners & Integrators  
- **Protocols integrating Synthetix products**:  
  - Utilize Synthetix liquidity for user-facing applications.  
  - Cover a wide range of use cases, generating trading fees for SNX stakers.  

### 5. The Greater Synthetix Ecosystem  
- Includes **additional protocols** closely tied to the Synthetix Community.  
- While not directly using Synthetix liquidity, they maintain strong relationships and shared goals with the Synthetix ecosystem.
  
# System Overview and Contract Review
## 1 basesynthetix.sol 
### System Overview
The BaseSynthetix contract is a foundational component of the Synthetix protocol, responsible for managing core token functionalities and integrating with other system components. The Synthetix protocol enables the creation and exchange of synthetic assets (Synths), providing exposure to various assets without direct ownership. This contract incorporates key mechanisms for token management, exchange operations, collateralization, and integration with other protocol components via the MixinResolver.
### Contract Review
The contract uses pragma solidity ^0.5.16, (line 1) which is outdated and potentially insecure. It is recommended to upgrade to Solidity ^0.8.x for enhanced security features like automatic overflow/underflow checks though the contract made use of safemath library.

## 2 sythetix.sol
The Synthetix contract is an integral component of the Synthetix protocol, a decentralized finance (DeFi) platform for trading synthetic assets. This contract extends the BaseSynthetix contract and incorporates additional functionality, such as managing interactions with reward escrow systems, supply schedules, and exchange mechanisms. It utilizes modular design principles and advanced Solidity features, such as ABIEncoderV2 (line-2)


### Key Features and Functionality

### 1. Inheritance and Address Resolver
- **Inheritance**: The contract inherits from `BaseSynthetix (line-13)`, ensuring it leverages common functionality while extending specific features.
- **Address Resolver**: The `resolverAddressesRequired on line 30-36` function adds additional dependencies (`RewardEscrow` and `SupplySchedule`) to the base set of required addresses. This function ensures the contract properly integrates additional dependencies (RewardEscrow and SupplySchedule) while maintaining existing functionalities
  
  ```solidity
  function resolverAddressesRequired() public view returns (bytes32[] memory addresses) {
        bytes32[] memory existingAddresses = BaseSynthetix.resolverAddressesRequired(); //basesynthetix (line-59-68)
        bytes32[] memory newAddresses = new bytes32[](2);
        newAddresses[0] = CONTRACT_REWARD_ESCROW;
        newAddresses[1] = CONTRACT_SUPPLYSCHEDULE;
        return combineArrays(existingAddresses, newAddresses);
    } 

### 2. Exchange Mechanisms
The contract facilitates several exchange mechanisms:
- **`exchangeWithVirtual (line 50-73)`**: Enables exchanges that create virtual synths.
  ```solidity
      function exchangeWithVirtual(
        bytes32 sourceCurrencyKey,
        uint sourceAmount,
        bytes32 destinationCurrencyKey,
        bytes32 trackingCode
    )
        external
        exchangeActive(sourceCurrencyKey, destinationCurrencyKey) // Ensures that the exchange is enabled for the provided source and destination currency keys.

        optionalProxy    // Allows the caller to act on behalf of another address if specified.
        returns (uint amountReceived, IVirtualSynth vSynth)
    {
        return
            exchanger().exchange(
                messageSender,
                messageSender,
                sourceCurrencyKey,
                sourceAmount,
                destinationCurrencyKey,
                messageSender,
                true,
                messageSender,
                trackingCode
            );
    }
  
- **`exchangeWithTrackingForInitiator (78-90)`**: Tracks exchanges for the initiating user, with the reward distribution address specified.

  ```solidity
  function exchangeWithTrackingForInitiator(
        bytes32 sourceCurrencyKey,
        uint sourceAmount,
        bytes32 destinationCurrencyKey,
        address rewardAddress,
        bytes32 trackingCode
    ) external exchangeActive(sourceCurrencyKey, destinationCurrencyKey) optionalProxy returns (uint amountReceived) {
        (amountReceived, ) = exchanger().exchange( // from basesynthetix and IExchanger
            messageSender,
            messageSender,
            sourceCurrencyKey,
            sourceAmount,
            destinationCurrencyKey,
            // solhint-disable avoid-tx-origin
            tx.origin,
            false,
            rewardAddress,
            trackingCode
        );
    }
  
- **`exchangeAtomically`**: Provides atomic exchange capabilities with a specified minimum return amount.
   ```solidity
    function exchangeAtomically(
        bytes32 sourceCurrencyKey,
        uint sourceAmount,
        bytes32 destinationCurrencyKey,
        bytes32 trackingCode,
        uint minAmount
    ) external exchangeActive(sourceCurrencyKey, destinationCurrencyKey) optionalProxy returns (uint amountReceived) {
        return
            exchanger().exchangeAtomically( // exchanger interface
                messageSender,
                sourceCurrencyKey,
                sourceAmount,
                destinationCurrencyKey,
                messageSender,
                trackingCode,
                minAmount
            );
    }



