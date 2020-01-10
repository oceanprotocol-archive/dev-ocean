---
title: Squid API Specification
description: The Squid API is a Level-2 API built on top of core Ocean components. It's a facilitator or enabler, but it's not the only way to interact with Ocean.
slug: /concepts/squid/
section: concepts
---

```
shortname: squid-spec
version: 0.5
status: Draft
date: January 2020
```

The goal of this doc is to help a developer build a version of the Squid API in any programming language. Currently, the Squid API is defined for Object-Oriented languages such as JavaScript, Java, and Python (which are the three initial implementation languages).

- [squid-js](https://github.com/oceanprotocol/squid-js)
- [squid-py](https://github.com/oceanprotocol/squid-py)
- [squid-java](https://github.com/oceanprotocol/squid-java)

## Ocean

This is the main class to interact with the Ocean network. 

All Ocean Protocol features are accessed through this module.

---

## ocean.assets

### create

High-level method publishing the metadata off-chain and registering the Service Agreement on-chain. It creates a DID Document (DDO) that represent the registered asset.

_Parameters_

```
        metadata: JSON object describing various attributes of the asset data (see below for sample metadata attributes)
publisherAccount: Account instance of who is publishing the asset
        services: list of Service instances (optional)
        accountOwner: If is specified, the ownership of the DID will be assigned to this ethereum account (optional)
```

_Returns_

`Asset object (encapsulates the DDO document)`

_Example_
```js
const metadata = {
  "assetId": "0x123412341234",
  "publisherId": "0x00000000000",
  "base":{
    "name": "name",
    "size": "5gb",
    "author": "someone",
    "license": "public domain",
    "contentType": '',
    "files": ["/url/dataset-name"],
  }
}

const asset = ocean.assets.create(metadata, publisherAccount, services=[ocean.services.createAccessSecretStoreService(...)], accountOwner)
```

The complete spec for assets metadata is here: [Asset Metadata Ontology](https://github.com/oceanprotocol/OEPs/tree/master/8)
 
---

### resolve

Given a DID, return the associated DID Document (DDO). The DDO is resolved by directly 
interacting with the keeper node to retrieve the metadata store's (Aquarius) URL. This URL is used to fetch the DDO from the off-chain ocean-db storage (MongoDB, BigchainDB, or whatever)

_Parameters_

```
did: str the asset did which consist of `did:op:` and the assetId hex str (without `0x` prefix)
```

_Returns_

`Asset object`

_Example_

```js
const asset = ocean.assets.resolve(did)
```

---

### transfer ownership

Given a DID, transfer the ownership to a new owner. This function only will work if is called by the DID owner.

_Parameters_

```
did: str the asset did which consist of `did:op:` and the assetId hex str (without `0x` prefix)
newOwner: the ethereum address of the new owner of the DID
```

_Returns_

`boolean specifying if the operation was executed successfully`

_Example_

```js
const result= ocean.assets.transferOwnership(did, newOwner)
```

---

### search

Given a search text query, returns a list of the Asset objects matching with that text query.

_Parameters_

```
text: str the text to search on
```

_Returns_

`list of Asset instances that match the search text`


_Example_

```js
const assets = ocean.assets.search('office')
```

---

### query

Search existing registered assets using more fine tuned method. Supports paging, 
sorting and search on specific metadata attributes. 

_Parameters_

```
searchQuery: JSON object 
```

```json
{
  "offset": 100,
  "page": 0,
  "query": {
    "value": 1
  },
  "sort": {
    "value": 1
  }
}
```

_Returns_

`list of Asset instances that match the search query`
 
_Example_

```js
const searchQuery = {"query": {"value": 1}}
const assets = ocean.assets.query(searchQuery)
```

---

### order

Start the purchase/order of an asset's service. Starts by signing the service 
agreement then sends the request to the publisher via the service endpoint (Brizo http service).

_Parameters_

```
                did: str the asset did which consist of `did:op:` and the assetId hex str (without `0x` prefix)
serviceDefinitionId: str id of the service within the asset DDO
    consumerAccount: Account instance of the consumer ordering the service
```

_Returns_

`agreementId (can be used to query the keeper regarding the order)`

_Example_

```js
const agreementId = ocean.assets.order(did, serviceDefinitionId, consumerAccount)
```

---

### consume

Downloads all files associated with the asset unless `index` is specified which will 
download only that one data file.

_Parameters_

```
        agreementId: hex str representation of `bytes32` id
                did: str the asset did which consist of `did:op:` and the assetId hex str (without `0x` prefix)
serviceDefinitionId: str id of the service within the asset DDO
    consumerAccount: Account instance of the consumer ordering the service
         resultPath: (optional) str path to store the downloaded files or results of a compute operation
         index: (optional) int index of specific file to download. The index 
            corresponds to the index in the `files` list in the DDO document.
```

_Returns_

`str local path on the file system where the asset data files are downloaded` 

_Example_

```js
const downloadsPath = ocean.assets.consume(agreementId, did, serviceDefinitionId, consumerAccount, resultPath)
```

---

### validate

Validate the integrity of an asset after consuming. This is achieved by regenerating 
the checksum of the ddo document and comparing to the registered checksum that was 
generated at time of publishing the asset. 

_Parameters_

```
  did: str the asset did which consist of `did:op:` and the assetId hex str (without `0x` prefix)
files: list of paths to downloaded files after completing asset purchase 
```

_Returns_

`boolean`

_Example_

```js
const asset_did = "did:op:000000000000000000000000000"
const isDataValid = ocean.assets.validate(asset_did, ["/path/to/file0", "/path/to/file1",])
```

---

### owner

_Parameters_

```
did: str the asset did which consist of `did:op:` and the assetId hex str (without `0x` prefix)
```

_Returns_

hex str -- the ethereum address of the owner/publisher of given asset did

_Example_

```js
const asset_did = "did:op:000000000000000000000000000"
const ownerAddress = ocean.assets.owner(asset_did)
```

---

### ownerAssets

_Parameters_

```
ownerAddress: hex str ethereum address of owner/publisher
```

_Returns_

List of Asset objects published by `ownerAddress`

_Example_
```js
const myAddress = "0x00000000000000000000000000000000"
const assets = ocean.assets.ownerAssets(myAddress)
```

---

### consumerAssets

_Parameters_

```
consumerAddress: hex str ethereum address of consumer
```

_Returns_

List of Asset objects purchased by `consumerAddress`

_Example_

```js
const myAddress = "0x00000000000000000000000000000000"
const assets = ocean.assets.consumerAssets(myAddress)
```

---

### delegatePermissions

For a existing asset, the owner of the asset delegate to a subject read or access permissions. This method will use the `msg.sender` as issuer.


_Parameters_

```
did: DID of the asset
subjectAddress: hex str the ethereum account address of the subject who will receive the permissions
```

_Returns_

`boolean`

_Example_

```js
const issuerAccount = ocean.accounts.list()[0]
const success = ocean.assets.delegatePermissions(
    did,
    subjectAddress, 
    )
```

---

### revokePermissions

For a existing asset, the owner of the asset revoke the access grants of a subject.
This method will use the `msg.sender` as issuer.


_Parameters_

```
did: DID of the asset
subjectAddress: hex str the ethereum account address of the subject who will receive the permissions
```

_Returns_

`boolean`

_Example_

```js
const issuerAccount = ocean.accounts.list()[0]
const success = ocean.assets.revokePermissions(
    did,
    subjectAddress, 
    )
```

---

### get permissions

Check if an user has permissions in a specific DID

_Parameters_

```
did: DID of the asset
subjectAddress: hex str the ethereum account address of the subject who will receive the permissions
```

_Returns_

- permissions: boolean true if has access and false if not

_Example_

```js
const permissions = ocean.assets.getPermissions(did, subjectAddress)
```

---


## ocean.accounts

### list

Returns all available accounts loaded via a wallet, or by Web3.

_Returns_

`list of Account instances`

_Example_

```js
const accounts = ocean.accounts.list()
```

---

### balance

Return tuple of both the etherBalance and oceanBalance

_Parameters_

```
account: Account instance
```

_Returns_

`(etherBalance: float, oceanBalance: int)`

_Example_

```js
const { etherBalance, oceanBalance } = ocean.accounts.balance(accounts[0])
```

---

### requestTokens

This is an alternative to `ocean.tokens.request()`

_Parameters_
```
account: Account instance
 amount: int number of tokens to be requested
```

_Returns_

`boolean`

_Example_

```js
const amount = 30
const account = ocean.accounts.list()[0]
const success = ocean.accounts.requestTokens(account, amount)
```

---

## ocean.compute

### execute

Executes a remote compute service associated with an asset and serviceAgreementId

_Parameters_

```
        agreementId: hex str representation of `bytes32` id
                did: str the asset did which consist of `did:op:` and the assetId hex str (without `0x` prefix)
serviceDefinitionId: str id of the service within the asset DDO
    consumerAccount: Account instance of the consumer ordering the service
        workflowDid: str the asset did (of `workflow` type) which consist of `did:op:` and the assetId hex str (without `0x` prefix)
```

_Returns_

`str The ID of the workflow.` 

_Example_

```js
const workflowId = ocean.compute.execute(agreementId, did, serviceDefinitionId, consumerAccount, workflowDid)
```

### status

Returns information about the status of a compute job

_Parameters_

```
agreementId: str The ID of the agreement.
workflowId: str The ID of the workflow.
```

_Returns_

```
{
  ok: true,
  status: 1,
  statusText: 'Workflow started.'
}
```

_Errors_

```
{
  ok: false,
  status: 404,
  statusText: 'Workflow does not exist.'
}
```

_Example_

```js
const jobStatus = ocean.compute.status(agreementId, workflowId)
```

### result

Returns the final result of a compute job published as an asset.

_Parameters_

```
agreementId: str The ID of the agreement.
workflowId: str The ID of the workflow.
```

_Returns_

`DDO`

_Example_

```js
const jobResult = ocean.compute.result(agreementId, workflowId)
```

---

## ocean.secret_store

This is a wrapper on top of the Secret Store defined in [Secret Store spec](https://github.com/oceanprotocol/dev-ocean/blob/master/doc/architecture/secret-store.md)

### encrypt

Encrypt the given text and store the encryption keys using the `documentId`. The encrypted text can be decrypted 
using the same keys identified by the `documentId`.

_Parameters_

```
      documentId: hex str (bytes32 id in hex str format) 
         content: str to be encrypted
publisherAccount: Account instance of document owner
```

_Returns_

`hex str (the encrypted text)`

_Example_

```js
const publisherAccount = ocean.accounts.list()[0]
const content = '/url/of/interesting-data.csv'
const documentId = 'did:op:1234123412341234'
const encryptedContent = ocean.secret_store.encrypt(documentId, content, publisherAccount)
```

---

### decrypt

Decrypt an encrypted text using the stored encryption keys associated with the documentId. Decryption requires that 
the account owner has access permissions for this `documentID`

_Parameters_

```
      documentId: hex str (bytes32 id in hex str format) 
encryptedContent: hex str the encrypted document that results from the `encrypt` call
 consumerAccount: Account instance requesting the decryption. Requires on-chain access authorization
```

_Returns_

`original decrypted text`

_Example_

```js
const consumerAccount = ocean.accounts.list()[0]
const documentId = 'did:op:1234123412341234'
ocean.secret_store.decrypt(documentId, encryptedContent, consumerAccount)
```

---

## ocean.tokens

### request
Request a number of Ocean Tokens.

_Parameters_

```
account: Account instance 
 amount: int number of tokens requested
```

_Returns_

`bool to indicate success/failure of the request`

_Example_

```js
account = ocean.accounts.list()[0]
const amount = ocean.tokens.request(account, 100)
```

### transfer

Transfer a number of tokens to the mentioned account.

_Parameters_

```
account: Account instance to receive the tokens
 amount: int number of tokens to transfer
```

_Returns_

`bool to indicate success/failure of the transfer`

_Example_

```js
const fromAccount = ocean.accounts.list()[0]
const receiverAddress = ocean.accounts.list()[1].address
ocean.tokens.transfer(receiverAddress, 20, fromAccount)
```

---

## ocean.templates

### propose

Suggest an agreement template smart contract to include in the white listed agreement templates.

_Parameters_

```
templateAddress: hex str the ethereum address of the deployed template (smart contract address)
        account: Account instance owner of the agreement template
```

_Returns_

`bool to indicate success/failure of the operation`

_Example_

```js
const templateAddress = '0x345645675678...'
ocean.templates.propose(templateId, ocean.accounts.list()[0])
```

---

### approve

Approve (whitelist) an already proposed template. Once a template is approved 
it can be used for creating agreements in Ocean Protocol keeper network.

_Parameters_

```
templateAddress: hex str the ethereum address of the deployed template (smart contract address)
        account: Account instance owner of the agreement template
```

_Returns_

`bool to indicate success/failure of the operation`

_Example_

```js
const templateAddress = '0x345645675678...'
ocean.templates.approve(templateId, ocean.accounts.list()[0])
```

---

### revoke

Cancel the propsed/approved template or essentially de-whitelist the template. This 
prevents the creation of any further agreements that are based on this template.

_Parameters_

```
templateAddress: hex str the ethereum address of the deployed template (smart contract address)
        account: Account instance owner of the agreement template
```

_Returns_

`bool to indicate success/failure of the operation`

_Example_

```js
const templateAddress = '0x345645675678...'
ocean.templates.revoke(templateId, ocean.accounts.list()[0])
```

---

## ocean.agreements

### prepare
Creates a consumer signature for the specified asset service.

_Parameters_

```
                did: str the asset did which consist of `did:op:` and the assetId hex str (without `0x` prefix)
serviceDefinitionId: str id of the service within the asset DDO 
    consumerAccount: Account instance of the consumer to sign the agreement
```

_Returns_

`(agreementId, signature)`

_Example_

```js
const { agreementId, signature } = ocean.agreements.prepare(did, serviceDefinitionId, consumerAccount)
```

---

### send 

Submit a service agreement to the publisher to create the agreement on-chain.

_Parameters_

```
                did: str the asset did which consist of `did:op:` and the assetId hex str (without `0x` prefix)
        agreementId: hex str representation of `bytes32` id
serviceDefinitionId: str id of the service within the asset DDO 
          signature: hex str the signature of the agreement hash (What is `agreement hash`)
    consumerAccount: Account instance of the consumer that signed the agreement
```

_Returns_

`None`

_Example_

```js
const did = 'did:op:123412341234'
const agreementId = ''
const serviceDefinitionId = ocean.assets.resolve(did).services[0].serviceDefinitionId
const consumerAddress = ocean.accounts.list()[0].address
const signature = '?'
ocean.agreements.send(did, agreementId, serviceDefinitionId, signature, consumerAccount)
```

---

### create

Create a service agreement on-chain. This should be called by the publisher of the asset. Consumer 
signature will be verified on-chain, but it is recommended to verify the signature in this method 
before submitting on-chain.

_Parameters_

```
                did: str the asset did which consist of `did:op:` and the assetId hex str (without `0x` prefix)
        agreementId: hex str representation of `bytes32` id
serviceDefinitionId: str id of the service within the asset DDO 
          signature: hex str the signature of the agreement hash (What is `agreement hash`)
    consumerAddress: hex str the ethereum account address of the consumer signing the agreement
            account: Account instance of the creator of this agreement. Can be the consumer, publisher, 
            provider, or any valid ethereum account.
```

_Returns_

`boolean`

_Example_

```js
const payload = {} // coming from the HTTP request
const { 
    did,
    serviceDefinitionId,
    agreementId,
    signature,
    consumerAddress } = payload
const myAccount = ocean.accounts.list()[0]
const success = ocean.agreements.create(
    did, 
    agreementId, 
    serviceDefinitionId, 
    signature, 
    consumerAddress, 
    myAccount
    )
```

---

### status

Get the status of a service agreement.

_Parameters_

```
agreementId: hex str representation of `bytes32` id
```

_Returns_

- json document with condition status of each of the agreement's conditions.

```json
{
    "agreementId": "",
    "conditions": {
        "lockReward": 1,
        "accessSecretStore": 1,
        "escrowReward": 1,
    }  
}
```
- None/null if agreement is invalid

_Example_

```js
const agreementId = ""
const agreementStatus = ocean.agreements.status(agreementId)
```

---

## ocean.agreements.conditions

### lockReward

Transfers tokens to the EscrowRewardCondition contract as an escrow payment. 
This is required before access can be given to the asset data.

_Parameters_
```
agreementId: hex str representation of `bytes32` id
      amount: int the price set for this service agreement
```

_Returns_

`bool success/failure`

_Example_

```js
const rewardLocked = ocean.agreements.conditions.lockReward(agreementId, amount)
```

---

### grantAccess

Authorize the consumer defined in the agreement to access (consume) this asset.

_Parameters_
```
agreementId: hex str representation of `bytes32` id
    assetId: hex str representation of `bytes32` id
    grantee: hexstr ethereum address of asset consumer
```

_Returns_

`bool success/failure`

_Example_

```js
ocean.agreements.conditions.grantAccess(agreementId, assetId, grantee)
```

### grantServiceExecution
Authorize the consumer defined in the agreement to execute a remote service associated with this asset.

_Parameters_
```
agreementId: hex str representation of `bytes32` id
    assetId: hex str representation of `bytes32` id
    grantee: hexstr ethereum address of asset consumer
```

_Returns_

`bool success/failure`

_Example_

```js
ocean.agreements.conditions.grantServiceExecution(agreementId, assetId, grantee)
```

---

### releaseReward

Transfer the escrow or locked tokens from the LockRewardCondition contract to the 
publisher's account. This should be allowed after access has been given to the consumer 
and the asset data is downloaded.

If the AccessSecretStoreCondition already timed out, this function will do a refund 
by transferring the token amount to the original consumer.

_Parameters_
```
agreementId: hex str representation of `bytes32` id
     amount: int the price set for this service agreement
```

_Returns_

`bool success/failure`

_Example_

```js
const rewardReleased = ocean.agreements.conditions.releaseReward(agreementId, amount)
```

---

### refundReward

Refund the escrow or locked tokens back to the consumer account. This will only work 
in the case where access was not granted within the specified timeout in the service 
agreement.

_Parameters_
```
agreementId: hex str representation of `bytes32` id
     amount: int the price set for this service agreement
```

_Returns_

`bool success/failure`

_Example_

```js
const rewardRefund = ocean.agreements.conditions.refundReward(agreementId, amount)
```


---


## Models

### Account
| attribute   | type       |
| :---------- | :--------- |
| address     | hex str    |

### Asset

| attribute   | type       |
| :---------- | :--------- |
| did         | str        |
| metadata    | json       |
| services    | Service [] |


### Service

| attribute            | type      |
| :------------------- | :-------- |
| type                 | string    |
| serviceDefinitionId  | string    |
| agreement            | Agreement |
| endpoints            | Endpoints |


### Agreement

| attribute   | type         |
| :---------- | :----------- |
| template    | hex str      |
| conditions  | dict[]/map[] |


### Endpoints

| attribute   | type       |
| :---------- | :--------- |
| service     | url str    |
| consume     | url str    |

---

### Error handling

Common error messages and error codes

TODO

## Examples

See the [_Example_ Code Using Squid](https://docs.oceanprotocol.com/tutorials/example-code/).

## Changelog

You can find older versions of the squid specifications in the [squid-specs folder](squid-specs).

### Squid Spec 0.5

Modifications:

* New `ocean.compute` methods

### Squid Spec 0.4

Modifications:

* New `ocean.assets.execute` method
* New `ocean.assets.delegatePermissions` method
* New `ocean.assets.revokePermissions` method
* New `ocean.assets.getPermissions` method
* New `ocean.agreements.conditions.grantServiceExecution` method
* `ocean.assets.create` allows to specify the address of the DID owner
* New `ocean.assets.transferOwnership` method allowing to transfer the ownership of a DID

This version of the squid specification is based in the OEP-7 v0.2.

### Squid Spec 0.3

New methods:

  - Get list of the Assets Published by an user
  - Get list of the Assets Received by an user
  - Get the SEA status
  - Get the Owner of an Asset
  - Expose an additional consume interface allowing to use `index` parameter
  - Consumer can initialize a SEA on-chain
  - Validate an Asset Data integrity


### Squid Spec 0.2

* Service Execution Agreements
* Assets Management
* Tokens request and transfer
* Secret Store API
