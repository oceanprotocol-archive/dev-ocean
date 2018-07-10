# ocean project map

This page serves as a map to the current project boards, milestones, and related resources.

Project boards:
1. [Business board](https://github.com/oceanprotocol/ocean/projects/1)
1. [Research board](https://github.com/oceanprotocol/ocean/projects/3)
1. [Development board](https://github.com/oceanprotocol/ocean/projects/2)


Milestones are here: https://github.com/oceanprotocol/ocean/milestones

## Current development
The current milestone is Plankton - Tethys Alpha (v0.1): https://github.com/oceanprotocol/ocean/milestone/1

This milestone is based on the user story in #39 Plankton mvp-2

Main ocean development repos:
* [keeper-contracts](https://github.com/oceanprotocol/keeper-contracts)
All of the solidity smart contracts that implement the keeper features.
* [plankton-frontend](https://github.com/oceanprotocol/plankton-frontend)
A simple web browser UI implemented with JavaScript for interacting with the ocean network. Supports publishing and consuming data assets.
* [provider-backend](https://github.com/oceanprotocol/provider-backend)
Handles off-chain database asset storage and serves data asset consumption requests.


## Keeper contracts (on-chain)
* Asset registration
* Asset purchase
* Access control (asset consumption authorization)

## Provider backend (ocean-db, assets registry and asset consumption)
* Asset registration
* Update asset metadata
* List/search assets
* Serve download requests and use on-chain authorization before granting access
* Support uploading data assets to store at provider end

## Front-end
* Search registered assets
* Display list of registered assets
* View asset metadata
* Purchase asset
* Download purchased asset
* Publish data asset
* Update asset metadata

