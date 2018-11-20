# ABI / Address Handling

## Abstract

This document describes how ocean currently handle contract Addresses and ABI's. We have to keep in mind that over time 
the different installations on the different chains will diverge. The development chain will have the latest version of contracts 
the POA AWS network will be updated afterwards, then kovan network then mainnet.

Different use cases: 
* development
* POA AWS aka integration
* kovan aka beta
* mainnet aka live

The requirements for the different use cases are different. The development chain (docker or local ganache) should be updated once the 
contracts have changed to enable development of the contracts and `squid-x`. 

## Status quo

### Structure

### Files
artifacts/*.json

OceanToken.kovan.json

OceanToken = name of the Artifact
kovan = name of the chain

i.e.
- OceanToken.poa_aws.json
- OceanToken.mainnet.json

### Content

```
{
  "abi": [...],
  "bytecode": "0x608060405234801561001057...",
  "address": "0x758Ec40785099b8A3b3EFBfD5EE79Cd1394424a0"
}
```

- abi: the current ABI of the contract
- bytecode: the contract content in bytecode representation
- address: the Address of the topmost instance on the Blockchain

We store tha abi together with the address for a specific chain on a specific time to bundle them up. [see](#issues/patterns) for details

https://github.com/oceanprotocol/keeper-contracts/blob/5b6f28e27b24479e241fb68958c793032545ed11/artifacts/OceanMarket.kovan.json

### Versioning
The whole `artifacts` folder is under version control on docker, maven, npm and pypi

That means `v0.3.22`
Contains the artifacts for development, poa_aws and kovan

That means `v1.0.0`
Contains the artifacts for development, poa_aws and kovan and mainnet

everthing in between 

## Features

* ABI versioning
* Address versioning

* Time travel possible by loading and old version of `keeper-contracts` e.g. `v0.3.21` to travel back to that point (address, abi 
combination) of all the chains (kovan, poa, mainnet)



docker

* no need for addresses or network in config of `squid-x`
* auto network detection available right now with metamask (switching to different networks without the 
need to reconfigured `squid-js``)
https://github.com/oceanprotocol/squid-js/blob/2cfb32199a384efdacdf45959ce5506a4bdc43c5/src/keeper/ContractHandler.ts#L9
https://github.com/oceanprotocol/squid-js/blob/2cfb32199a384efdacdf45959ce5506a4bdc43c5/src/keeper/Keeper.ts#L38

### Usage

* use bytecode in `squid-js` to redeploy contracts to local ganache with different addresses to 
enable unit tests of contracts. https://github.com/oceanprotocol/squid-js/blob/2cfb32199a384efdacdf45959ce5506a4bdc43c5/test/keeper/TestContractHandler.ts#L77
* putting Ethereum addresses in config files is an anti pattern like putting ip addresses in 
config files is one

## Issues / Patterns

* ABI and Address constellation have to be exactly precise
    if you mix up older or newer ABI's with Addresses you are going to have a bad time
    The result could be catastrophic
    
* ABI and Address could be versioned separated:
    ABI could be versioned how they are right now in `keeper-contracts` release for npm, maven, docker and pypi
    how do we release the different addresses for the different Blockchains (at least 4) then?
    how do we version them?
    how do we update them?

* How to get ABI and Address from the docker instance?
    * Change it that you dont need it to get
    
* ABI / Address combination is not persisted on Docker that means you need to get the addresses out of the docker on running it
    * make docker persistant
    * use persistant blockchain and migrate the contracts on docker build instead of on docker run
    * use kovan or poa testet from docker as default instead of ganache
    
* Never use ganache-cli for running stuff, it's only for unit testing of `keeper-contracts` and `squid-x`
    it is not persistant 
    addresses will change on every start
    mess