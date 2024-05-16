# Actors, Privileges, and Upgradeable Procedures Documentation

## Introduction

This document provides a comprehensive overview of the actors involved in the smart contract system and outlines their respective privileges and roles.
Different `roles` (we call them `domain`) are granted via the `AddressManager` contract's `setAddress()` function. The concept is similar to Optimism's `AddressManager`, except that we use `chainId + domainName` as the key for a given address. This approach is required for bridging, as the destination chain's bridge address needs to be included with the message hash to protect against tampering. Every contract that relies on role-based authentication, will inherit its `roles` from the `AddressResolver` contract, which serves as a 'middleman/lookup' by querying the `AddressManager` to determine if a given address is allowed to act on behalf of that domain.

## 1. Domains (≈role per chainId)

In the context of smart contracts, all actors within the system have specific roles, each with distinct responsibilities and privileges. If there are modifiers such as `onlyFromNamed` or `onlyFromNamed2`, it means access is granted based solely on the aforementioned `AddressResolver` and `AddressManager` contracts. There can be up to two domains in a single function for the purposes of granting access (although in some rare situations this could be set up in different way, such as in a `DAO`, ).

### 1.1 Taiko

- **Role**: Granted to the TaikoL1 smart contract.
- **Privileges**:
  - Mint/burn the Taiko token
  - Mint/burn ERC20 tokens (I think this privilege needs to go)

### 1.2 Bridge

- **Role**: Granted for smart-contract bridging (on both chains)).
- **Privileges**:
  - Trigger token minting/transfer (ERC20, ERC721, ERC1155) from vault contracts on the destination chain
  - Trigger the release of locked assets on the source chain if the bridging process fails.

### 1.3 ERCXXX_Vault

- **Role**: Granted to corresponding token vault contracts (ERC20, ERC721, ERC1155)
- **Privileges**:
  - Bridging, minting and burning the corresponding tokens (but no autonomous minting/burning).

## 2. Different access modifiers

In addition to the `onlyFromNamed` and `onlyFromNamed2` modifiers mentioned earlier, the following modifiers are available:

### 2.1 onlyOwner

- **Description**: Only owner can be granted access.
- **Associated contracts**: TaikoToken, AddressManager

### 2.2 onlyAuthorized

- **Description**: Can be granted only to owner-approved entities; the address is a smart contract (in our case, `Bridge`).

## 3. Upgradable Procedures

The smart contract system features upgradable procedures designed with flexibility and security in mind. These procedures are based on the following principles:

### 3.1 Deployment Scripts

- Deployment scripts are located in the `packages/protocol/scripts` folder. Their purpose is to facilitate deployment and maintenance, as well as provide a point of reference.

### 3.2 Transparent Upgradeability

Upgradeability is based on the Transparent Upgradeability Proxy by OpenZeppelin, ensuring that contract upgrades are secure and transparent to all stakeholders.

- A tool called Transparent Upgradability Proxy made by OpenZeppelin helps to make sure that contract upgrades are carried out in a secure and transparent way

### 3.3 Ownership Transition

- Currently, on testnets, some privileges (such as onlyOwner) are assigned to externally owned accounts (EOAs) for easier testing. However, it's important to note that TimelockController contracts will assume ownership at a later stage.

## Conclusion

Actors and their privileges, combined with robust upgradeable procedures, is essential for products based on smart contract based, such as based rollups. Clear and unambgious documentation helps to ensure that all stakeholders understand their roles and responsibilities within the system, which promotes its adaptability and security over time.

Please help to ensure that this document is updated regularly as with relevant and up-to-date information about actors, their roles and role-based privileges.