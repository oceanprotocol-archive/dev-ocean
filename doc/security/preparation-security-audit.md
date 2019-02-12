# Preparation for Security Audit

Table of Contents
=================

   * [Preparation for Security Audit](#preparation-for-security-audit)
   * [Table of Contents](#table-of-contents)
      * [Goal of the security review](#goal-of-the-security-review)
      * [Components](#components)
         * [Keeper Contracts](#keeper-contracts)
            * [MultiSig Wallet](#multisig-wallet)
            * [Deployment](#deployment)
         * [Token Bridge](#token-bridge)
            * [Deployment](#deployment-1)
         * [Secret Store](#secret-store)


## Goal of the security review

Whe need to have information about:

* Is the Ocean Token secure?
* Are the user transactions facilitated using the Service Execution Agreements secure?
* Is the governance model for deployment, upgrades and roles inside the contracts secure?
* What's the overall level of security?
* Are we okay for the next network launch?


## Components

### Keeper Contracts

```
Url: https://github.com/oceanprotocol/keeper-contracts/
Topic: Solidity Smart Contracts
Version: 0.7
Branch: develop
```

It provides the core business logic of Ocean Protocol including the following modules:

* Service Execution Agreements (SEA) - Generic framework to facilitate data services
* Ocean Token - Ocean Protocol ERC20 Token Contract
* Dispenser - Ocean Tokens Dispenser
* DIDRegistry - Registration of Decentralized Identifiers (W3C DID)

High-Level Architecture:
* [Service Execution Agreements](https://blog.oceanprotocol.com/exploring-the-sea-service-execution-agreements-65f7523d85e2)
* [Secure On-Chain Access Control for Ocean Protocol](https://blog.oceanprotocol.com/secure-on-chain-access-control-for-ocean-protocol-38dca0af820c)
* [OEP11: On-Chain Access Control using Service Agreements](https://github.com/oceanprotocol/OEPs/tree/master/11)
* [OEP7: Decentralized Identifiers](https://github.com/oceanprotocol/OEPs/tree/master/7)

Some information about SEA:

* [Modular Service Execution Agreements](https://github.com/oceanprotocol/OEPs/issues/125)
* [Service Conditions](https://github.com/oceanprotocol/OEPs/issues/119)
* [HashLock Condition](https://github.com/oceanprotocol/OEPs/issues/120)
* [Sign Condition](https://github.com/oceanprotocol/OEPs/issues/121)
* [LockReward Condition](https://github.com/oceanprotocol/OEPs/issues/122)
* [SEA Template Store](https://github.com/oceanprotocol/OEPs/issues/132)


Additional Details:

* Travis is used as Continuous Integration pipeline: https://travis-ci.com/oceanprotocol/keeper-contracts
* Security reports (mythril + securify) are generated automatically when merging to `develop` branch. They will be stored [here](https://github.com/oceanprotocol/keeper-contracts-security-reports)
* Project integrates ZeppelinOS 2.0 for upgradability
* Roles are managed using standard openzeppelin-eth project libraries
* All contracts are Ownable to transfer ownership and leverage the `onlyOwner` modifier.
* Project is using solidity v0.5.3

#### MultiSig Wallet

Cleaned and modularized for of the Gnosis MultiSigWallet v1.4.0
https://github.com/oceanprotocol/MultiSigWallet

Can be used in conjunction with: https://wallet.gnosis.pm

#### Deployment

Two instances of the MultiSigWallet are required. They are set up automatically by the deploment script. They can be used by importing the addresses in https://wallet.gnosis.pm

To deploy the contracts use:

`npm run deploy` to deploy all the contracts.
`npm run deploy OceanToken` to only deploy the OceanToken contract.

This will deploy the contracts to a node located at localhost:8545. Most likely `ganache-cli`.

To upgrade a contract use:

`npm run upgrade OceanToken2:OceanToken` to swap out the implementation of `OceanToken` with the implementation of the `OceanToken2` contract.

This will issue an `upgrade request` against the MultiSigWallet that has to be confirmed by the owners of the wallet. Once this is done the implementations are swapped out.

### Token Bridge

```
Url: https://github.com/oceanprotocol/poc-token-bridge/
Topic: Solidity Smart Contracts
Branch: master
```

Ocean Token Bridge is used to transfer ERC20 tokens between Ocean POA network and Ethereum main-net. It is based on token bridge developed by POA.network.

We are using the following components. We have copied some of those from POA.network repositories for testing:

[POA Token Bridge](https://github.com/poanetwork/token-bridge)
[POA Bridge Contracts](https://github.com/poanetwork/poa-bridge-contracts)
[OceanToken](https://github.com/oceanprotocol/keeper-contracts/blob/release/v0.7/contracts/OceanToken.sol) (this one should be taken from `keeper-contracts` rather than from the bridge repo)

This repo worked as PoC of the token bridge. But for further deployment it's necessary to use the source code coming from POA.network.

#### Deployment

Instructions can be found on: https://github.com/oceanprotocol/token-bridge#3-how-to-deploy


### Secret Store
```
Url: https://github.com/paritytech/parity-ethereum
Topic: 
Branch: master
```

Parity Secret Store is a feature included as part of the Parity Ethereum client that allows users to store a fragmented ECDSA key on the blockchain, such that retrievals are controlled by a permissioned Smart Contract.

The Secret Store implements a threshold retrieval system, so individual Secret Store nodes are unable to reconstruct the keys to decrypt documents by themselves. A Secret Store node only saves a portion of the ECDSA key. The decryption key can only be generated if a consensus is reached by an amount of Secret Store nodes bigger than the threshold that the publisher of the secret chooses.

We use the Parity Secret Store for encrypting URLs to data. Those URLs only can be decrypted by the consumer when the SEA conditions are fulfilled (typically after payment). In the decryption flow, the Secret Store call the `checkPermissions` function of the AccessConditions contract to check if a specific user (consumer `address`) is entitled to decrypt the URLs of a specific Asset (`did`). 


https://github.com/oceanprotocol/dev-ocean/blob/master/doc/architecture/secret-store.md



