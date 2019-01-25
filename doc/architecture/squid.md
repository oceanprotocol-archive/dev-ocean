# Squid API

The Squid API is a Level-2 API built on top of core Ocean components. It's a facilitator or enabler, but it's not the only way to interact with Ocean.

**Note 1: "Provider" was renamed to Aquarius and some of its functionality was moved over to Brizo. This page hasn't been updated to reflect that change yet.**

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
* [ocean.agreements.conditions.payment](#ocean.agreements.conditions.payment)
* [ocean.agreements.conditions.access](#ocean.agreements.conditions.access)


## Ocean

This is the main class to interact with the Ocean network. 

| Method                             | Return Value            | Python | JS   | Java |
| :--------------------------------- | :---------------------- | :----- | :--- | :--- |
| Ocean.getInstance() / Ocean()      | Ocean instance             |        |      |      |

All ocean protocol features are accessed through this module.

---

## ocean.assets

| Method    | Return Value              | Python | JS   | Java |
| :-------- | :-------------------------| :----- | :--- |:---- |
| create    | Asset                     |        |      |      | 
| resolve   | Asset                     |        |      |      |
| search    | Asset[]                   |        |      |      |
| query     | Asset[]                   |        |      |      |
| consume   | str downloaded file path  |        |      |      |
| order     | hex str agreement id      |        |      |      |

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
const metadata = {...}
const asset = ocean.assets.create(metadata, publisherAccount, services=[ocean.services.createAccessService(...)])
```

The metadata is a json object that contains the following structure:

```json
{
  "assetId":,
  "publisherId":,
  "base":{
    "name":,
    "size":,
    "author":,
    "license":,
    "contentType":,
    "contentUrls":,
    "price":,
    "type":
  }
}
```

There are more optional fields that you can check in the [provider API](https://github.com/oceanprotocol/aquarius/blob/develop/aquarius/app/assets.py).

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

Parameters
```
        agreementId:  
                did: str the asset did which consist of `did:op:` and the assetId hex str (without `0x` prefix)
serviceDefinitionId: str id of the service within the asset DDO
    consumerAccount: Account instance of the consumer ordering the service
               mode: ?
```

Returns

`str local path on the file system where the asset data files are downloaded` 

Example
```js
const downloadsPath = ocean.assets.consume(agreementId, did, serviceDefinitionId, consumerAccount, mode)
```

---

## ocean.accounts

| Method              | Return Value                        | Python | JS   | Java |
| :------------------ | :-----------------------------------| :----- | :--- |:---- |
| list                | Account list                        |        |      |      |
| balance             | tuple(ether_balance, ocean_balance) |        |      |      |
| request_tokens      |                                     |        |      |      |


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

| Method         | Return Value              | Python | JS   | Java |
| :------------- | :----------------------   | :----- | :--- |:---- |
| encrypt        | string encrypted_content  |        |      |      |
| decrypt        | string content            |        |      |      |

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

| Method      | Return Value      | Python | JS   | Java |
| :---------- | :---------------- | :----- | :--- |:---- |
| request     | bool True/False   |        |      |      |
| transfer    | bool True/False   |        |      |      |

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

| Method             | Return Value            | Python | JS   | Java |
| :----------------- | :---------------------- | :----- | :--- |:---  |
| create             | hex str template id     |        |      |      |

#### create
Create a service agreement template and deploy it on-chain so it can be used in service agreements.

Parameters
```
     account: Account instance
templateJson: JSON definition of agreement template (refer to OEP-11 for full spec)
  templateId: hex str representation of `bytes32` id of the template
```

Returns

`bool to indicate success/failure of the transfer`

Example
```js
const templateJson = {
    conditions: [],
    
}
const templateId = '0x345645675678...'
ocean.templates.create(ocean.accounts.list()[0], templateJson, templateId)
```

---

## ocean.agreements

| Method              | Return Value              | Python | JS   | Java |
| :------------------ | :------------------------ | :----- | :--- |:---- |
| prepare             | (agreementId, signature)  |        |      |      |
| send                | -                         |        |      |      |
| create              | boolean                   |        |      |      |

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
const { agreementId, signature } = ocean.agreements.create(did, serviceDefinitionId, consumerAccount)
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
    consumerAddress: hex str the ethereum account address of the consumer signing the agreement
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
ocean.agreements.send(did, agreementId, serviceDefinitionId, signature, consumerAddress)
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
   publisherAccount: Account instance of the publisher of this asset
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
const publisherAccount = ocean.accounts.list()[0]
const success = ocean.agreements.create(
    did, 
    agreementId, 
    serviceDefinitionId, 
    signature, 
    consumerAddress, 
    publisherAccount
    )
```

---

## ocean.agreements.conditions.payment

| Method           | Return Value   | Python | JS   | Java |
| :--------------- | :------------- | :----- | :--- |:---- |
| lock             | boolean        |        |      |      |
| release          | boolean        |        |      |      |
| refund           | boolean        |        |      |      |

#### lock
Transfers tokens to the PaymentConditions contract account as an escrow payment. 
This is required before access can be given to the asset data.

Parameters
```
agreementId: hex str representation of `bytes32` id
    assetId: hex str representation of `bytes32` id
      price: int the price set for this service agreement
```

Returns

`bool success/failure`

Example
```js
const payment_locked = ocean.agreements.conditions.payment.lock(agreementId, assetId, price)
```

---

#### release
Transfer the escrow or locked tokens from the PaymentConditions contract account to the 
publisher's account. This should be allowed after access has been given to the consumer 
and the asset data is downloaded.

Parameters
```
agreementId: hex str representation of `bytes32` id
    assetId: hex str representation of `bytes32` id
      price: int the price set for this service agreement
```

Returns

`bool success/failure`

Example
```js
const payment_released = ocean.agreements.conditions.payment.release(agreementId, assetId, price)
```

---

#### refund
Return the escrow or locked tokens back to the consumer account. 

Refund requires the following events:
Price is already paid to the escrow contract (i.e. `lockPayment` condition is already fulfilled) 
Access condition `timeout` is exceeded without access having been given

Parameters
```
agreementId: hex str representation of `bytes32` id
    assetId: hex str representation of `bytes32` id
      price: int the price set for this service agreement
```

Returns

`bool success/failure`

Example
```js
const payment_refund = ocean.agreements.conditions.payment.refund(agreementId, assetId, price)
```

---

## ocean.agreements.conditions.access

| Method        | Return Value            | Python | JS   | Java |
| :------------ | :---------------------- | :----- | :--- |:---- |
| grant         | boolean                 |        |      |      |

#### grant
Authorize the consumer defined in the agreement to access (consume) this asset.

Parameters
```
agreementId: hex str representation of `bytes32` id
    assetId: hex str representation of `bytes32` id
```

Returns

`bool success/failure`

Example
```js
ocean.agreements.conditions.access.grant(agreementId, assetId)
```

---

## ocean.services

| Method                             | Return Value            | Python | JS   | Java |
| :--------------------------------- | :---------------------- | :----- | :--- |:---- |
| create_access_service              | Service instance        |        |      |      |

#### createAccessService
Creates an `Access` type service to be included in asset DDO.

Parameters
```
           price: int number of tokens
service_endpoint: str url of service endpoint
consume_endpoint: str url of consume endpoint
```

Returns

`Service instance`

Example
```js
const service = ocean.services.createAccessService(
    25, 
    'http://brizo/services/access/initialize', 
    'http://brizo/services/access/consume'
    )
```


---

Models

### Account

| attribute   | type       | Python | JS   | Java |
| :---------- | :--------- | :----- | :--- |:--- |
| address     | hex str    |        |    |      |


### Asset

| attribute   | type       | Python | JS   | Java |
| :---------- | :--------- | :----- | :--- |:---- |
| did         | str        |        |      |      |
| metadata    | json       |        |      |      |
| services    | Service [] |        |      |      |


### Service

| attribute            | type      | Python | JS   | Java |
| :------------------- | :-------- | :----- | :--- |:---- |
| type                 | string    |        |      |      |
| serviceDefinitionId  | string    |        |      |      |
| agreement            | Agreement |        |      |      |
| endpoints            | Endpoints |        |      |      |


### Agreement

| attribute   | type         | Python | JS   | Java |
| :---------- | :----------- | :----- | :--- |:---  |
| template    | hex str      |        |      |      |
| conditions  | dict[]/map[] |        |      |      |


### Endpoints

| attribute   | type       | Python | JS   | Java |
| :---------- | :--------- | :----- | :--- |:---- |
| service     | url str    |        |      |      |
| consume     | url str    |        |      |      |

---

### Error handling
Common error messages and error codes

TODO


## Examples

For examples, see the page listing [Tools & Examples on the Ocean Protocol docs site](https://docs.oceanprotocol.com/concepts/tools/).
