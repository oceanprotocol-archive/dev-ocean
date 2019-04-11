# Squid API
```
name: Squid API Specification
shortname: squid-spec
version: 0.3
status: Draft
```

The Squid API is a Level-2 API built on top of core Ocean components. It's a facilitator or enabler, but it's not the only way to interact with Ocean.

- [squid-js](https://github.com/oceanprotocol/squid-js)
- [squid-py](https://github.com/oceanprotocol/squid-py)
- [squid-java](https://github.com/oceanprotocol/squid-java)

The goal of this doc is to help a developer build a version of the Squid API in any programming language. Currently, the Squid API is defined for Object-Oriented languages such as JavaScript, Java, and Python (which are the three initial implementation languages).

---

**🐲🦑 THERE BE DRAGONS AND SQUIDS. This is in alpha state and you can expect running into problems. If you run into them, please open up [a new issue](https://github.com/oceanprotocol/dev-ocean/issues). 🦑🐲**

---


## Summary of modules
* [ocean](#ocean)
* [ocean.assets](#ocean.assets)
* [ocean.accounts](#ocean.accounts)
* [ocean.secret_store](#ocean.secret_store)
* [ocean.tokens](#ocean.tokens)
* [ocean.templates](#ocean.templates)
* [ocean.services](#ocean.services)
* [ocean.agreements](#ocean.agreements)
* [ocean.agreements.conditions](#ocean.agreements.conditions)


## Changelog

You can find older versions of the squid specifications in the [squid-specs folder](squid-specs).

### Squid Spec 0.3 (latest)

* New methods
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

## Ocean

This is the main class to interact with the Ocean network. 

All ocean protocol features are accessed through this module.

---

## ocean.assets

#### create

High-level method publishing the metadata off-chain and registering the Service Agreement on-chain. It creates a 
DID Document (DDO) that represent the registered asset.

Parameters
```
        metadata: JSON object describing various attributes of the asset data (see below for sample metadata attributes)
publisherAccount: Account instance of who is publishing the asset
        services: list of Service instances (optional)
```

Returns

`Asset object (encapsulates the DDO document)`

Example
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

const asset = ocean.assets.create(metadata, publisherAccount, services=[ocean.services.createAccessSecretStoreService(...)])
```

The complete spec for assets metadata is here: [Asset Metadata Ontology](https://github.com/oceanprotocol/OEPs/tree/master/8)
 
---

#### resolve

Given a DID, return the associated DID Document (DDO). The DDO is resolved by directly 
interacting with the keeper node to retrieve the metadata store's (Aquarius) URL. This URL is 
used to fetch the DDO from the off-chain ocean-db storage (mongodb, bigchain-db, or whatever)

Parameters
```
did: str the asset did which consist of `did:op:` and the assetId hex str (without `0x` prefix)
```

Returns

`Asset object`

Example
```js
const asset = ocean.assets.resolve(did)
```

---

#### search

Given a search text query, returns a list of the Asset objects matching with that text query.

Parameters
```
text: str the text to search on
```

Returns

`list of Asset instances that match the search text`


Example
```js
const assets = ocean.assets.search('office')
```

---

#### query

Search existing registered assets using more fine tuned method. Supports paging, 
sorting and search on specific metadata attributes. 

Parameters
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

Returns

`list of Asset instances that match the search query`
 
Example
```js
const searchQuery = {"query": {"value": 1}}
const assets = ocean.assets.query(searchQuery)
```

---

#### order

Start the purchase/order of an asset's service. Starts by signing the service 
agreement then sends the request to the publisher via the service endpoint (Brizo http service)

Parameters
```
                did: str the asset did which consist of `did:op:` and the assetId hex str (without `0x` prefix)
serviceDefinitionId: str id of the service within the asset DDO
    consumerAccount: Account instance of the consumer ordering the service
```

Returns

`agreementId (can be used to query the keeper regarding the order)`

Example
```js
const agreementId = ocean.assets.order(did, serviceDefinitionId, consumerAccount)
```

---

#### consume
Downloads all files associated with the asset unless `index` is specified which will 
download only that one data file.

Parameters
```
        agreementId: hex str representation of `bytes32` id
                did: str the asset did which consist of `did:op:` and the assetId hex str (without `0x` prefix)
serviceDefinitionId: str id of the service within the asset DDO
    consumerAccount: Account instance of the consumer ordering the service
         resultPath: (optional) str path to store the downloaded files or results of a compute operation
         index: (optional) int index of specific file to download. The index 
            corresponds to the index in the `files` list in the DDO document.
```

Returns

`str local path on the file system where the asset data files are downloaded` 

Example
```js
const downloadsPath = ocean.assets.consume(agreementId, did, serviceDefinitionId, consumerAccount, resultPath)
```

---

#### validate
Validate the integrity of an asset after consuming. This is achieved by regenerating 
the checksum of the ddo document and comparing to the registered checksum that was 
generated at time of publishing the asset. 

Parameters
```
  did: str the asset did which consist of `did:op:` and the assetId hex str (without `0x` prefix)
files: list of paths to downloaded files after completing asset purchase 
```

Returns

`boolean`


Example
```js
const asset_did = "did:op:000000000000000000000000000"
const isDataValid = ocean.assets.validate(asset_did, ["/path/to/file0", "/path/to/file1",])
```

---

#### owner

Parameters
```
did: str the asset did which consist of `did:op:` and the assetId hex str (without `0x` prefix)
```

Returns

hex str -- the ethereum address of the owner/publisher of given asset did

Example
```js
const asset_did = "did:op:000000000000000000000000000"
const ownerAddress = ocean.assets.owner(asset_did)
```

---

#### ownerAssets

Parameters
```
ownerAddress: hex str ethereum address of owner/publisher
```

Returns

List of Asset objects published by `ownerAddress`

Example
```js
const myAddress = "0x00000000000000000000000000000000"
const assets = ocean.assets.ownerAssets(myAddress)
```

---

#### consumerAssets

Parameters
```
consumerAddress: hex str ethereum address of consumer
```

Returns

List of Asset objects purchased by `consumerAddress`

Example
```js
const myAddress = "0x00000000000000000000000000000000"
const assets = ocean.assets.consumerAssets(myAddress)
```

---

## ocean.accounts

#### list

Returns all available accounts loaded via a wallet, or by Web3.

Parameters

Returns

`list of Account instances`


Example
```js
const accounts = ocean.accounts.list()
```

---

#### balance
Return tuple of both the etherBalance and oceanBalance

Parameters
```
account: Account instance
```

Returns

`(etherBalance: float, oceanBalance: int)`

Example
```js
const { etherBalance, oceanBalance } = ocean.accounts.balance(accounts[0])
```

---

#### requestTokens
This is an alternative to `ocean.tokens.request()`

Parameters
```
account: Account instance
 amount: int number of tokens to be requested
```

Returns

`boolean`

Example
```js
const amount = 30
const account = ocean.accounts.list()[0]
const success = ocean.accounts.requestTokens(account, amount)
```

---

## ocean.secret_store
This is a wrapper on top of the Secret Store defined in [Secret Store spec](https://github.com/oceanprotocol/dev-ocean/blob/master/doc/architecture/secret-store.md)

#### encrypt
Encrypt the given text and store the encryption keys using the `documentId`. The encrypted text can be decrypted 
using the same keys identified by the `documentId`.

Parameters
```
      documentId: hex str (bytes32 id in hex str format) 
         content: str to be encrypted
publisherAccount: Account instance of document owner
```

Returns

`hex str (the encrypted text)`

Example
```js
const publisherAccount = ocean.accounts.list()[0]
const content = '/url/of/interesting-data.csv'
const documentId = 'did:op:1234123412341234'
const encryptedContent = ocean.secret_store.encrypt(documentId, content, publisherAccount)
```

---

#### decrypt
Decrypt an encrypted text using the stored encryption keys associated with the documentId. Decryption requires that 
the account owner has access permissions for this `documentID`

Parameters
```
      documentId: hex str (bytes32 id in hex str format) 
encryptedContent: hex str the encrypted document that results from the `encrypt` call
 consumerAccount: Account instance requesting the decryption. Requires on-chain access authorization
```

Returns

`original decrypted text`

Example
```js
const consumerAccount = ocean.accounts.list()[0]
const documentId = 'did:op:1234123412341234'
ocean.secret_store.decrypt(documentId, encryptedContent, consumerAccount)
```

---

## ocean.tokens

#### request
Request a number of Ocean Tokens.

Parameters
```
account: Account instance 
 amount: int number of tokens requested
```

Returns

`bool to indicate success/failure of the request`

Example
```js
account = ocean.accounts.list()[0]
const amount = ocean.tokens.request(account, 100)
```

#### transfer
Transfer a number of tokens to the mentioned account.

Parameters
```
account: Account instance to receive the tokens
 amount: int number of tokens to transfer
```

Returns

`bool to indicate success/failure of the transfer`

Example
```js
const fromAccount = ocean.accounts.list()[0]
const receiverAddress = ocean.accounts.list()[1].address
ocean.tokens.transfer(receiverAddress, 20, fromAccount)
```

---

## ocean.templates

#### propose
Suggest an agreement template smart contract to include in the white listed agreement templates.

Parameters
```
templateAddress: hex str the ethereum address of the deployed template (smart contract address)
        account: Account instance owner of the agreement template
```

Returns

`bool to indicate success/failure of the operation`

Example
```js
const templateAddress = '0x345645675678...'
ocean.templates.propose(templateId, ocean.accounts.list()[0])
```

---

#### approve
Approve (whitelist) an already proposed template. Once a template is approved 
it can be used for creating agreements in Ocean Protocol keeper network.

Parameters
```
templateAddress: hex str the ethereum address of the deployed template (smart contract address)
        account: Account instance owner of the agreement template
```

Returns

`bool to indicate success/failure of the operation`

Example
```js
const templateAddress = '0x345645675678...'
ocean.templates.approve(templateId, ocean.accounts.list()[0])
```

---

#### revoke
Cancel the propsed/approved template or essentially de-whitelist the template. This 
prevents the creation of any further agreements that are based on this template.

Parameters
```
templateAddress: hex str the ethereum address of the deployed template (smart contract address)
        account: Account instance owner of the agreement template
```

Returns

`bool to indicate success/failure of the operation`

Example
```js
const templateAddress = '0x345645675678...'
ocean.templates.revoke(templateId, ocean.accounts.list()[0])
```

---

## ocean.agreements

#### prepare
Creates a consumer signature for the specified asset service.

Parameters
```
                did: str the asset did which consist of `did:op:` and the assetId hex str (without `0x` prefix)
serviceDefinitionId: str id of the service within the asset DDO 
    consumerAccount: Account instance of the consumer to sign the agreement
```

Returns

`(agreementId, signature)`

Example
```js
const { agreementId, signature } = ocean.agreements.prepare(did, serviceDefinitionId, consumerAccount)
```

---

#### send 
Submit a service agreement to the publisher to create the agreement on-chain.

Parameters
```
                did: str the asset did which consist of `did:op:` and the assetId hex str (without `0x` prefix)
        agreementId: hex str representation of `bytes32` id
serviceDefinitionId: str id of the service within the asset DDO 
          signature: hex str the signature of the agreement hash (What is `agreement hash`)
    consumerAccount: Account instance of the consumer that signed the agreement
```

Returns

`None`

Example
```js
const did = 'did:op:123412341234'
const agreementId = ''
const serviceDefinitionId = ocean.assets.resolve(did).services[0].serviceDefinitionId
const consumerAddress = ocean.accounts.list()[0].address
const signature = '?'
ocean.agreements.send(did, agreementId, serviceDefinitionId, signature, consumerAccount)
```

---

#### create
Create a service agreement on-chain. This should be called by the publisher of the asset. Consumer 
signature will be verified on-chain, but it is recommended to verify the signature in this method 
before submitting on-chain.

Parameters
```
                did: str the asset did which consist of `did:op:` and the assetId hex str (without `0x` prefix)
        agreementId: hex str representation of `bytes32` id
serviceDefinitionId: str id of the service within the asset DDO 
          signature: hex str the signature of the agreement hash (What is `agreement hash`)
    consumerAddress: hex str the ethereum account address of the consumer signing the agreement
            account: Account instance of the creator of this agreement. Can be the consumer, publisher, 
            provider, or any valid ethereum account.
```

**Return**

`boolean`

Example
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

#### status
Get the status of a service agreement.

Parameters
```
agreementId: hex str representation of `bytes32` id
```

Returns
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

Example
```js
const agreementId = ""
const agreementStatus = ocean.agreements.status(agreementId)
```

---

## ocean.agreements.conditions

#### lockReward
Transfers tokens to the EscrowRewardCondition contract as an escrow payment. 
This is required before access can be given to the asset data.

Parameters
```
agreementId: hex str representation of `bytes32` id
      amount: int the price set for this service agreement
```

Returns

`bool success/failure`

Example
```js
const rewardLocked = ocean.agreements.conditions.lockReward(agreementId, amount)
```

---

#### grantAccess
Authorize the consumer defined in the agreement to access (consume) this asset.

Parameters
```
agreementId: hex str representation of `bytes32` id
    assetId: hex str representation of `bytes32` id
    grantee: hexstr ethereum address of asset consumer
```

Returns

`bool success/failure`

Example
```js
ocean.agreements.conditions.grantAccess(agreementId, assetId, grantee)
```

---

#### releaseReward
Transfer the escrow or locked tokens from the LockRewardCondition contract to the 
publisher's account. This should be allowed after access has been given to the consumer 
and the asset data is downloaded.

If the AccessSecretStoreCondition already timed out, this function will do a refund 
by transferring the token amount to the original consumer.

Parameters
```
agreementId: hex str representation of `bytes32` id
     amount: int the price set for this service agreement
```

Returns

`bool success/failure`

Example
```js
const rewardReleased = ocean.agreements.conditions.releaseReward(agreementId, amount)
```

---

#### refundReward
Refund the escrow or locked tokens back to the consumer account. This will only work 
in the case where access was not granted within the specified timeout in the service 
agreement.

Parameters
```
agreementId: hex str representation of `bytes32` id
     amount: int the price set for this service agreement
```

Returns

`bool success/failure`

Example
```js
const rewardRefund = ocean.agreements.conditions.refundReward(agreementId, amount)
```

---

## ocean.services

#### createAccessSecretStoreService
Creates an `Access` type service to be included in asset DDO.

Parameters
```
          price: int number of tokens
serviceEndpoint: str url of service endpoint
consumeEndpoint: str url of consume endpoint
```

Returns

`Service instance`

Example
```js
const service = ocean.services.createAccessSecretStoreService(
    25, 
    'http://brizo/services/access/initialize', 
    'http://brizo/services/access/consume'
    )
```


---

Models

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

For examples, see the page listing [Tools & Examples on the Ocean Protocol docs site](https://docs.oceanprotocol.com/concepts/tools/).

## Implementation status

ocean

| Method                             | Return Value            | Python | JS   | Java |
| :--------------------------------- | :---------------------- | :----- | :--- | :--- |
| Ocean.getInstance() / Ocean()      | Ocean instance          |        |      |      |

---

ocean.assets

| Method              | Return Value              | Python | JS   | Java |
| :------------------ | :------------------------ | :----- | :--- |:---- |
| create              | Asset                     |        |      |      | 
| resolve             | Asset                     |        |      |      |
| search              | Asset[]                   |        |      |      |
| query               | Asset[]                   |        |      |      |
| consume             | str downloaded file path  |        |      |      |
| order               | hex str agreement id      |        |      |      |
| validate            | boolean                   |        |      |      |
| owner               | hex str ethereum address  |        |      |      |
| ownerList           | Asset[]                   |        |      |      |
| consumerList        | Asset[]                   |        |      |      |

---

ocean.accounts

| Method              | Return Value                        | Python | JS   | Java |
| :------------------ | :-----------------------------------| :----- | :--- |:---- |
| list                | Account list                        |        |      |      |
| balance             | tuple(etherBalance, oceanBalance) |        |      |      |
| requestTokens       | boolean (success/failure)           |        |      |      |

---

ocean.secret_store

| Method         | Return Value              | Python | JS   | Java |
| :------------- | :----------------------   | :----- | :--- |:---- |
| encrypt        | string encrypted_content  |        |      |      |
| decrypt        | string content            |        |      |      |

---

ocean.tokens

| Method      | Return Value      | Python | JS   | Java |
| :---------- | :---------------- | :----- | :--- |:---- |
| request     | bool True/False   |        |      |      |
| transfer    | bool True/False   |        |      |      |

---

ocean.templates

| Method             | Return Value            | Python | JS   | Java |
| :----------------- | :---------------------- | :----- | :--- |:---  |
| create             | hex str template id     |        |      |      |

---

ocean.agreements

| Method              | Return Value              | Python | JS   | Java |
| :------------------ | :------------------------ | :----- | :--- |:---- |
| prepare             | (agreementId, signature)  |        |      |      |
| send                | -                         |        |      |      |
| create              | boolean                   |        |      |      |
| status              | json                      |        |      |      |

---

ocean.agreements.conditions

| Method           | Return Value   | Python | JS   | Java |
| :--------------- | :------------- | :----- | :--- |:---- |
| lockReward       | boolean        |        |      |      |
| grantAccess      | boolean        |        |      |      |
| releaseReward    | boolean        |        |      |      |
| refundReward     | boolean        |        |      |      |

---

ocean.services

| Method                             | Return Value            | Python | JS   | Java |
| :--------------------------------- | :---------------------- | :----- | :--- |:---- |
| createAccessSecretStoreService                | Service instance        |        |      |      |

