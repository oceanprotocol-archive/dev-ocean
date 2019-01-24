# Squid API

The Squid API is a Level-2 API built on top of core Ocean components. It's a facilitator or enabler, but it's not the only way to interact with Ocean.

**Note 1: "Provider" was renamed to Aquarius and some of its functionality was moved over to Brizo. This page hasn't been updated to reflect that change yet.**

**Note 2: This document was created to help the developers of squid-js, squid-py and squid-java develop a consistent API, but it hasn't kept up with those implementations. For the latest actually-implemented APIs, refer to their repos:**

- [squid-js](https://github.com/oceanprotocol/squid-js)
- [squid-py](https://github.com/oceanprotocol/squid-py)
- [squid-java](https://github.com/oceanprotocol/squid-java)

The goal of this doc is to help a developer develop a version of the Squid API in any programming language. Currently, the Squid API is defined for Object-Oriented languages such as JavaScript, Java, and Python (which are the three initial implementation languages).

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

This class serves as the interface with Ocean Protocol. 

| Method                             | Return Value            | Python | JS   | Java |
| :--------------------------------- | :---------------------- | :----- | :--- | :--- |
| Ocean.getInstance() / Ocean()      | Ocean                   |        |    |      |

All ocean protocol features are access through this module.


## ocean.assets

| Method                             | Return Value            | Python | JS   | Java |
| :--------------------------------- | :---------------------- | :----- | :--- |:--- |
| create | Asset |  |        |    | 
| resolve | Asset |  |  |  |
| search | Asset[] |  |  |  |
| query | Asset[] |  |  |  |
| consume | str downloaded file path |  |  |  |
| order | hex str agreement id |  |  |  |

#### create

ASYNC. High-level method publishing the metadata off-chain and registering the Service Agreement on-chain. It creates a 
DID Document (DDO) that represent the registered asset.

`Parameters`:
*  

`Return`: Asset object (encapsulates the DDO document)

```js
const metadata = {...}
const asset = ocean.assets.create(metadata, publisherAccount, services=[ocean.services.createAccessService(...), ...])
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


#### resolve

ASYNC. Given a DID, return the associated DID Document (DDO). The DDO is resolved by directly 
interacting with the keeper node to retrieve the metadata store's (Aquarius) URL. This URL is 
used to fetch the DDO from the off-chain ocean-db storage (mongodb, bigchain-db, or whatever)

`Parameters`:
*  

`Return`: Asset object

```js
const asset = ocean.assets.resolve(did)
```

#### search

ASYNC. Given a search text query, returns a list of the Asset objects matching with that text query.

`Parameters`:
*  

`Return`: 

```
const ddoList = ocean.assets.search('office')
```
You have to do a request to this endpoint:
GET {provider.url}/api/v1/aquarius/assets/ddo/query
This method is expecting a json object that contains the following structure:

 ```
{
        "offset": 100,
        "page": 0,
        "sort": {
          "value": 1
        },
        "text": "Office"
}
 ```
 
#### query

ASYNC. Given a search query, returns a list of the Asset objects matching with that query.

```js
const ddoList = ocean.assets.query(searchQuery)
```

You have to do a request to this endpoint:
POST {provider.url}/api/v1/aquarius/assets/ddo/query
This method is expecting a json object that contains the following structure:

`Parameters`:
*  

`Return`: 

 ```
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

#### order

ASYNC. Start the purchase/order of an asset's service. It first signs the service agreement then sends the request 
to the publisher (Brizo http service)

`Parameters`:
*  

`Return`: 

```js
const serviceAgreementId = ocean.assets.order(did, serviceDefinitionId, consumerAccount)
```

#### consume

`Parameters`:
*  

`Return`: 

```js
const downloadedFilesPath = ocean.assets.consume(agreementId, did, serviceDefinitionId, consumerAccount, mode)
```


## ocean.accounts

| Method                             | Return Value            | Python | JS   | Java |
| :--------------------------------- | :---------------------- | :----- | :--- |:--- |
| list | Account list|    |      | |
| balance | tuple(ether_balance, ocean_balance) |  |  |  |
| request_tokens |  |  |  |  |


#### list

ASYNC. Returns all available accounts loaded via a wallet, or by Web3.

`Parameters`:
*  

`Return`: Account list


```js
const accounts = ocean.accounts.list()
```

#### balance
Return: tuple of both the etherBalance and oceanBalance

`Parameters`:
*  

`Return`: 

```js
const { etherBalance, oceanBalance} = ocean.accounts.balance(accounts[0])
```


#### requestTokens
This is an alternative to `ocean.tokens.request()`

`Parameters`:
*  

`Return`: 

```js
const amount = 30
const account = ocean.accounts.list()[0]
ocean.accounts.requestTokens(account, amount)
```


## ocean.secret_store

| Method                             | Return Value            | Python | JS   | Java |
| :--------------------------------- | :---------------------- | :----- | :--- |:--- |
| encrypt | string encrypted_content |    |      | |
| decrypt | string content |  |  |  |

#### encrypt
Encrypt the given text and store the encryption keys using the `documentId`. The encrypted text can be decrypted 
using the same keys identified by the `documentId`.

`Parameters`:
* documentId: hex str (bytes32 id in hex str format) 
* content: str to be encrypted
* publisherAccount: Account instance of document owner

`Return`: hex str (the encrypted text)

```js
const publisherAccount = ocean.accounts.list()[0]
const content = '/url/of/interesting-data.csv'
const documentId = 'did:op:1234123412341234'
const encryptedContent = ocean.secret_store.encrypt(documentId, content, publisherAccount)
```

#### decrypt
Decrypt an encrypted text using the stored encryption keys associated with the documentId. Decryption requires that 
the account owner has access permissions for this `documentID`

`Parameters`:
* documentId: hex str (bytes32 id in hex str format) 
* encryptedContent: hex str the encrypted document that results from the `encrypt` call
* consumerAccount: Account instance requesting the decryption. Requires on-chain access authorization

`Return`: original decrypted text

```js
const consumerAccount = ocean.accounts.list()[0]
const documentId = 'did:op:1234123412341234'
ocean.secret_store.decrypt(documentId, encryptedContent, consumerAccount)
```


## ocean.tokens

| Method                             | Return Value            | Python | JS   | Java |
| :--------------------------------- | :---------------------- | :----- | :--- |:--- |
| request | bool True/False |    |      | |
| transfer | bool True/False |  |  |  |

##### request
ASYNC. Request a number of Ocean Tokens.

`Parameters`:
* account: Account instance 
* amount: int number of tokens requested

`Return`: bool to indicate success/failure of the request

```js
account = ocean.accounts.list()[0]
const amount = ocean.tokens.request(account, 100)
```

#### transfer
Transfer a number of tokens to the mentioned account.

`Parameters`:
* account: Account instance to receive the tokens
* amount: int number of tokens to transfer

`Return`: bool to indicate success/failure of the transfer

```js
const fromAccount = ocean.accounts.list()[0]
const receiverAddress = ocean.accounts.list()[1].address
ocean.tokens.transfer(receiverAddress, 20, fromAccount)
```


## ocean.templates

| Method                             | Return Value            | Python | JS   | Java |
| :--------------------------------- | :---------------------- | :----- | :--- |:--- |
| create | hex str template id |    |      | |

#### create
Create a service agreement template and deploy it on-chain so it can be used in service agreements.

`Parameters`:
* account: Account instance
* templateJson: JSON definition of agreement template (refer to OEP-11 for full spec)
* templateId: hex str representation of `bytes32` id of the template

`Return`: bool to indicate success/failure of the transfer

```js
const templateJson = {
    conditions: [],
    
}
const templateId = '0x345645675678...'
ocean.templates.create(ocean.accounts.list()[0], templateJson, templateId)
```


## ocean.agreements

| Method                             | Return Value            | Python | JS   | Java |
| :--------------------------------- | :---------------------- | :----- | :--- |:--- |
| prepare | (agreementId, signature) |    |      | |
| send | | None |  |  |
| create | boolean |    |      | |

#### prepare
Creates a consumer signature for the specified asset service.

`Parameters`:
* did: str the asset did which consist of `did:op:` and the assetId hex str (without `0x` prefix)
* serviceDefinitionId: str id of the service within the asset DDO 
* consumerAccount: Account instance of the consumer to sign the agreement

`Return`: (agreementId, signature)

```js
const { agreementId, signature } = ocean.agreements.create(did, serviceDefinitionId, consumerAccount)

```

#### send 
Submit a service agreement to the publisher to create the agreement on-chain.

`Parameters`:
* did: str the asset did which consist of `did:op:` and the assetId hex str (without `0x` prefix)
* agreementId: hex str representation of `bytes32` id
* serviceDefinitionId: str id of the service within the asset DDO 
* signature: hex str the signature of the agreement hash (What is `agreement hash`)
* consumerAddress: hex str the ethereum account address of the consumer signing the agreement

`Return`: None

```js
const did = 'did:op:123412341234'
const agreementId = ''
const serviceDefinitionId = ocean.assets.resolve(did).services[0].serviceDefinitionId
const consumerAddress = ocean.accounts.list()[0].address
const signature = '?'
ocean.agreements.send(did, agreementId, serviceDefinitionId, signature, consumerAddress)
```

#### create
Create a service agreement on-chain. This should be called by the publisher of the asset. Consumer 
signature will be verified on-chain, but it is recommended to verify the signature in this method 
before submitting on-chain.

`Parameters`:
* did: str the asset did which consist of `did:op:` and the assetId hex str (without `0x` prefix)
* agreementId: hex str representation of `bytes32` id
* serviceDefinitionId: str id of the service within the asset DDO 
* signature: hex str the signature of the agreement hash (What is `agreement hash`)
* consumerAddress: hex str the ethereum account address of the consumer signing the agreement
* publisherAccount: Account instance of the publisher of this asset

Return: boolean

```js
const payload = {} // coming from the HTTP request
const { 
    did,
    serviceDefinitionId,
    agreementId,
    signature,
    consumerAddress } = payload
const publisherAccount = ocean.accounts.list()[0]
const success = ocean.agreements.create(did, agreementId, serviceDefinitionId, signature, consumerAddress, publisherAccount)
```


## ocean.agreements.conditions.payment

| Method                             | Return Value            | Python | JS   | Java |
| :--------------------------------- | :---------------------- | :----- | :--- |:--- |
| lock | boolean         |    |      | |
| release | boolean |  |  |  |
| refund | boolean |  |  |  |

#### lock
Transfers tokens to the PaymentConditions contract account as an escrow payment. 
This is required before access can be given to the asset data.

`Parameters`:
* agreementId: hex str representation of `bytes32` id
* assetId: hex str representation of `bytes32` id
* price: int the price set for this service agreement
`Return`: bool success/failure

```js
const payment_locked = ocean.agreements.conditions.payment.lock(agreementId, assetId, price)
```

#### release
Transfer the escrow or locked tokens from the PaymentConditions contract account to the 
publisher's account. This should be allowed after access has been given to the consumer 
and the asset data is downloaded.

`Parameters`:
* agreementId: hex str representation of `bytes32` id
* assetId: hex str representation of `bytes32` id
* price: int the price set for this service agreement

`Return`: bool success/failure

```js
const payment_released = ocean.agreements.conditions.payment.release(agreementId, assetId, price)
```

#### refund
Return the escrow or locked tokens back to the consumer account. Refund can only proceed 
after the agreement `timeout` is exceeded without access having been given. Another 
requirement for refund is that tokens are already been locked for this 
agreement (i.e. `lockPayment` condition is already fulfilled). 

`Parameters`:
* agreementId: hex str representation of `bytes32` id
* assetId: hex str representation of `bytes32` id
* price: int the price set for this service agreement

`Return`: bool success/failure

```js
const payment_refund = ocean.agreements.conditions.payment.refund(agreementId, assetId, price)
```


## ocean.agreements.conditions.access

| Method                             | Return Value            | Python | JS   | Java |
| :--------------------------------- | :---------------------- | :----- | :--- |:--- |
| grant | boolean |    |      | |

#### grant
Authorize the consumer defined in the agreement to access (consume) this asset.

`Parameters`:
* agreementId: hex str representation of `bytes32` id
* assetId: hex str representation of `bytes32` id

`Return`: bool success/failure

```js
ocean.agreements.conditions.access.grant(agreementId, assetId)
```

## ocean.services

| Method                             | Return Value            | Python | JS   | Java |
| :--------------------------------- | :---------------------- | :----- | :--- |:--- |
| create_access_service |          |    |      | |

#### createAccessService

`Parameters`:
*  

`Return`: 

```js

```


Models

Account

| attribute   | type       | Python | JS   | Java |
| :---------- | :--------- | :----- | :--- |:--- |
| address     | hex str    |        |    |      |

Asset

| attribute   | type       | Python | JS   | Java |
| :---------- | :--------- | :----- | :--- |:--- |
| did         | str        |        |    |      |
| metadata    | json       |        |    |      |
| services    | Service [] |        |    |      |

Service

| attribute            | type | Python | JS   | Java |
| :------------------- | :----- | :----- | :--- |:--- |
| agreement            | Agreement |      | |    |
| endpoints            | Endpoints |      | |    |

Agreement

| attribute   | type       | Python | JS   | Java |
| :---------- | :--------- | :----- | :--- |:--- |
| template   | hex str   |   |   |   |
| conditions | dict[]/map[]   |   |   |   |

Endpoints

| attribute   | type       | Python | JS   | Java |
| :---------- | :--------- | :----- | :--- |:--- |
| service    | url str |   |   |   |
| consume    | url str |   |   |   |


## Common error messages and error codes
TODO


## Examples

For examples, see the page listing [Tools & Examples on the Ocean Protocol docs site](https://docs.oceanprotocol.com/concepts/tools/).
