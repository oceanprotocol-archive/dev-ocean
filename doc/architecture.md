
Table of Contents
=================

   * [Architecture](#architecture)
      * [Components](#components)
         * [Frontend](#frontend)
         * [Keeper Smart Contracts](#keeper-smart-contracts)
         * [Provider](#provider)
      * [Interactions](#interactions)
         * [Assets registering and consumption](#assets-registering-and-consumption)
         * [On-Chain Access Control](#on-chain-access-control)
      * [Project Repositories](#project-repositories)


---


This page is a central point for documenting the Ocean Architecture

# Architecture

In the below image you can see a diagram of the initial High-Level Ocean Network Architecture:

![High-Level Architecture](architecture/img/high-level-architecture.png)

In the above diagram you can see the following components:

- **Frontend** - Application implemented using HTML + Javascript + CSS, running in the client side (user's browser).
- **Keeper Contracts** - Solidity Smart Contracts running on the decentralized Ethereum Virtual Machine (EVM).
- **Provider** - Backend application giving some network advanced services. Initially:
  - On-Chain access control
  - Metadata storage
  - Gathering of service proofs


## Components

### Frontend

The **Frontend** application implement the following high-level functionalities:

- **Publishing** - Allowing to the user to publish new Assets to the network
- **Consuming** - Allowing to the user to list and consume the Asset's published
- **Marketplace** - Enabling complex interactions and advanced capabilities like:
  - Search and discovery of assets
  - Filtering
  - Registering of users using specific KYC processes

The frontend application will be running in the client side and will communicate with the following external components:

 - **Smart Contracts**. Enabling the interaction with the Ocean Smart Contracts that provide the Market business logic. This integration is implemented using the [Ethereum Javascript API (web3.js)](https://github.com/ethereum/web3.js/).
 - **Provider**. Enabling he access to the Asset's purchased to the consumers, and allowing the Metadata management of assets to the publishers. This communication happens using HTTP API's.

 The frontend application will be subscribed to the EVM transaction log, allowing to receive asynchronous messages. This allows to trigger automatic actions when some events are raised (ie. the download of an Asset is triggered automatically when the purchase has been confirmed).

![Frontend High-Level Architecture](architecture/img/frontend-hl-arch.png)


### Keeper Smart Contracts

Solidity Smart Contracts deployed and running in the decentralized [Ethereum Virtual Machine (EVM)](https://github.com/ethereum/wiki/wiki/Ethereum-introduction#about-ethereum).

Ocean Keeper implementation where we put the following modules together:

- **TCRs** - Users create challenges and resolve them through voting to maintain registries
- **Ocean Tokens** - The intrinsic tokens circulated inside Ocean network, which is used in the voting of TCRs;
- **Curated Proofs Market** - The core marketplace where people can transact with each other and curate assets through staking with Ocean tokens.

![Contracts Architecture](architecture/img/contracts-structure.jpg)


### Provider

Application running in the backend side (Python) enabling some network advanced capabilities:

- Metadata management - Abstracting the access to different Metadata stores. Providers can integrate different metadata repositories. The Oceandb plugin system allows to integrate different datastores implementing the oceandb interfaces.
- Gathering of service proofs - Enabling to retrieve different kind of service proofs of different providers. For example, allowing to retrieve receipt's from cloud providers to validate service delivery
- On-Chain access control - The provider is in charge of validate on-chain that a consumer is entitled to get access to an asset or service. This happens integrating the keeper from the provider side.

The high-level architecture can be seen in the following diagram:

![Provider High-Level Architecture](architecture/img/provider-hl-arch.png)

## Interactions

### Assets registering and consumption

![Assets Registering & Consumption Flow](architecture/img/assets-registering.png)

The lower level details about the consumption are included in the On-Chain Access Control section.

### On-Chain Access Control

![On-Chain Access Control Flow](architecture/img/onchain-acl.png)

## Project Repositories

The Ocean Github project repositories can be found in the [repositories section](architecture/repos.md).


