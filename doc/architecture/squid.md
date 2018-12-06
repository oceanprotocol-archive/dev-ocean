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

## Common

Common functions exposed by an abstract class extended by all the other core classes

```java
/**
 * Abstract class providing the common methods like initilizers and similar
*/
abstract class OceanBase {}

/**
 * Access to Ocean functionalities
*/
class Ocean {}

/**
 * Extends OceanBase providing Asset functionalities
*/
class Asset extends OceanBase {
    id: string
    publisherId: string
    price: number
}

/**
 * Extends OceanBase providing Account functionalities
*/
class Account extends OceanBase  {
    name: string
    balance: Balance
}

/**
 * Extends OceanBase providing Service Agreement functionalities
*/
class ServiceAgreement extends OceanBase {
    tbd
}

/**
 * Extends OceanBase providing Order functionalities
*/
class Order extends OceanBase {
    id: string
    asset: Asset
    assetId: string
    timeout: number
    pubkey: string
    key: any
    paid: boolean
    status: number
}

/**
 * Extends OceanBase providing SecretStore functionalities
*/
class SecretStore extends OceanBase {
}

/**
 * Balance Model
*/
class Balance {
    eth: number
    ocn: number
}

```

### Getting an instance

```java
getInstance(web3Dto, providerDto)
```

#### Parameters

* `web3Dto` - Web3 client wrapped in a DTO object to avoid coupling with specific web3 specific libraries
* `providerDto` - Ocean Provider client wrapped in a DTO object

This method returns an instance of the **Ocean** class. It allows to get access to the other Ocean core methods.

### Instantiate an Ocean object

In Python:

```python
ocean = Ocean('config.ini')


```

In JavaScript:

```js
import { Ocean } from '@oceanprotocol/squid'

const ocean = await Ocean.getInstance({...})
```

## Ocean

This class serves as the interface with Ocean Protocol. The Ocean class aggregates the list of **Account**s 
(ethereum accounts), list of **Asset**s, and a list of **Order** objects. The Ocean class aggregates the 
Keeper class, which in turn interfaces with the running Smart Contracts: Token, ServiceAgreements, PaymentConditions, etc.

Account/wallet:
* Javascript: relies on Metamask wallet/account management. Transactions will prompt the user for approval
* Python: `Ocean` has a main account which defaults to the first account in web3.eth.accounts. The main account can be set 
using `ocean.set_main_account(address, password)`. If the password is not set or invalid, all keeper (on-chain) transactions 
will fail unless the account is unlocked which is not recommended
* Java: TBD

### getAccounts

ASYNC. Returns all available accounts loaded via a wallet, or by Web3.

```js
array[Account] = ocean.getAccounts()
```

### searchAssets

ASYNC. Given a search query, returns a list of the Asset objects matching with that query.

```js
array[Asset] = ocean.searchAssets(searchQuery)
```

You have to do a request to this endpoint:
POST {provider.url}/api/v1/aquarius/assets/ddo/query
This method is expecting a json object that contains the following structure:

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

### searchAssetsByText

ASYNC. Given a search text query, returns a list of the Asset objects matching with that text query.

```
array[Asset] = ocean.searchAssetsByText(searchQuery)
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

### searchOrders

(*TBD*) SYNC. Return a list of orders by search query. **Nice to Have**.

```js
array[Order] = ocean.searchOrders(searchQuery)
```

### getOrdersByAccount

ASYNC. Returns a list of orders by given account.

```js
array[Order] = ocean.getOrdersByAccount(account)
```

### registerAsset

ASYNC. High-level method publishing the metadata in a DDO (DID document) off-chain and registering 
the asset DID on-chain. This creates an asset DID and DDO document that encapsulate the ownership, 
metadata (as a service), and other services available with this asset (e.g. Access or Compute service agreements)

```js
asset_did = ocean.registerAsset(metadata, publisherAddress, serviceDescriptors)
```

### resolveDID

ASYNC. Given a DID, return the associated DID Document (DDO). The DDO is resolved by directly 
interacting with the keeper node. The DDO itself is stored off-chain in ocean-db (mongodb, bigchain-db, or whatever)

```js
DDO = ocean.resolveDID(did)
```

### getOrder

ASYNC. Get an order from the given orderId.

```js
Order = ocean.getOrder(orderId)
```


### getAsset

ASYNC. Get an asset based on its identifier, in the form of a DID. 

```js
asset = ocean.getAsset(assetDID)
```

### getServiceAgreement

SYNC. Returns as serviceAgreement object based from the service agreement id (Publisher). _Nice to Have_.

```js
serviceAgreement = ocean.getServiceAgreement(serviceAgreementId)
```

## Account

Ocean Account object.

### getId

ASYNC. Return the Id used by this account.

```js
id= account.getId()
```

### getOceanBalance

ASYNC. Returns the Ocean Tokens balance for that account.

```js
oceanBalance= account.getOceanBalance()
```

### getEtherBalance

ASYNC. Returns the Ether balance for that account.

```js
ethBalance= account.getEtherBalance()
```

### getBalance

ASYNC. Returns an instance of the Balance object for that account.

```js
balance= account.getBalance()
```

### requestTokens

ASYNC. Request a number of Ocean Tokens. Returns the number of tokens accounted.

```js
amount= account.requestTokens(amountTokens)
```

### unlock
ASYNC. Unlocks the eth account if password is provided (Python only). The unlock 
applies only to the next transaction, the account locks automatically after the 
transaction.

```python
account.unlock()
``` 

## Asset

Interface provides access to assets.

### getId

SYNC. Return the ID used by this asset.

```js
id= order.getId()
```

### purchase

ASYNC. Given an Asset ID/DID or Asset object and Service Agreement, the Consumer created for the  class purchases an asset

```js
order = asset.purchase(assetDID, serviceAgreementId, timeout)
```

### getDDO

SYNC. Return the created DDO used by this asset.

```js
ddo = asset.getDDO()
```

### getDID

SYNC. Return the DID used by this asset.

```js
did = asset.getDID()
```

### publishMetadata

SYNC. Given an asset metadata, register this metadata using the assets DDO off-chain. It returns a boolean with the result of the operation.

```js
status= asset.publishMetadata(metadata)
```

You have to do a request to this endpoint:

```text
POST {provider.url}/api/v1/provider/assets/metadata
```

This method is expecting a json object that contains the following structure:

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

In the base object there are more optional fields that you can check in the [provider API](https://github.com/oceanprotocol/aquarius/blob/develop/aquarius/app/assets.py).

### getMetadata

SYNC. returns the metadata stored off chanin by using the asset DDO. Internally calls Ocean.resolveDID(did).

```js
metadata= asset.getMetadata()
```

You have to do a request to this endpoint:

```text
GET {provider.url}/api/v1/provider/assets/metadata/<asset_id>
```

### updateMetadata

SYNC. Given an asset metadata, update the metadata using the asset DDO off-chain. This method replace the complete existing DDO by the DDO provided. It returns a boolean with the result of the operation.

```js
status= asset.updateMetadata(metadata)
```

You have to do a request to this endpoint:

```text
PUT {provider.url}/api/v1/provider/assets/metadata
```

This method is expecting a json object that contains the following structure:

```json
{
    "assetId": ,
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
    },
    "curation":{
        "rating": ,
        "numVotes":
    }
}
```

In the base object there are more optional fields that you can check in the [provider API](https://github.com/oceanprotocol/aquarius/blob/develop/aquarius/app/assets.py).

### retireMetadata

ASYNC. Given an Asset metadata, this method delete logically the Asset Metadata. **Nice to Have**

```js
asset.retireMetadata(metadata)
```

### getServiceAgreements

SYNC. Get a list of the service agreements published for this asset. providerId could be optional. _Nice to Have_

```js
[serviceAgreementIds] = asset.getServiceAgreements(providerId)
```

## ServiceAgreement

Interface provides access to ServiceAgreement functions.

### getId

SYNC. Return the ID used by this serviceAgreement.

```js
id = serviceAgreement.getId()
```

### getPrice

SYNC. Given a service agreement ID, gets the asset price information existing on-chain.

```js
price = serviceAgreement.getPrice(serviceAgreementId)
tbd
```

### getStatus

SYNC. Returns the status of a Service Agreement.

Possible values are:

- `0` => Pending
- `1` => Enabled
- `2` => Retired

```js
status = serviceAgreement.getStatus()
```

### publish

ASYNC. The Publisher register a Service Agreement related with the Asset, returns a ServiceAgreement object

```js
serviceAgreement = serviceAgreement.publish(providerId, price, ..)
```

### retire

ASYNC. Given a Service Agreement object, the Publisher retire a Service Agreement. It changes the status to the value `2` => Retired.

```js
result = serviceAgreement.retire()
```

### getAccess

SYNC. Get all the information required to get access to an asset after the purchase process

```js
access = serviceAgreement.getAccess()
```

## Order

Interface provides access to Order functions

### getId

SYNC. Return the ID used by this order.

```js
id= order.getId()
```

### getStatus

ASYNC. Using an order object, returns an integer representing the order status as defined in the keeper. It is described as an Enum.

Possible values are:

- `0` => Pending
- `1` => Paid
- `2` =>Canceled

```js
status= order.getStatus()
```

### commit

ASYNC. Publisher commits this order with the access token.

```js
isCommited= order.commit(accessToken)
```

### pay

ASYNC. Pay for this order with given account.

```js
paymentId= order.pay(consumerAccount)
```

### verifyPayment

SYNC. Given an order object, verifies if the order was paid.

```js
status= order.verifyPayment()
```

### consume

ASYNC. Get all the information required to get access to an asset after the purchase process, using the internal `assetId` and `serviceAgreementId`.

```js
url= order.consume(consumerAccount)
```

## SecretStore (Private)

Interface provides access to SecretStore functions

### encryptDocument

SYNC. **Private function** encapsulated as part of the `register` function.

It integrates the Parity Ethereum & Secret Store API allowing to encrypt a document.

Given by a **Publisher** an unique resource id (DID), the document to encrypt and the Secret Store cluster threshold (could be pre-defined to a fixed number), integrate the Secret Store APIs to encrypt the document.

```js
encryptedDocument= secretStore.encryptDocument(did, document, threshold)
```

### decryptDocument

SYNC.  **Private function** encapsulated as part of the `getAssetAccess` function. 

It integrates the Parity Ethereum & Secret Store API allowing to decrypt a document if the user executing this function is authorized by a Smart Contract.
Given by a **Consumer** an unique resource id (DID) and the document encrypted, this functions integrates the Secret Store API's to decrypt the document. The on-chain authorization phase is transparent for the user.

```js
document= secretStore.decryptDocument(did, encryptedDocument)
```

## Squid API Implementation state

Public API

| Class            | Method                             | Return Value            | Prio | Python Implementation | Javascript Implementation | Java Implementation |
| :--------------- | :--------------------------------- | :---------------------- | :--- | :-------------------- | :------------------------ | :------------------ |
| Ocean            | getInstance (js, java)/ Ocean (py) | Ocean                   | High | x                     | x                         | Not Implemented     |
| Ocean            | getAccounts                        | array[Account]          | High | x                     | x                         | Not Implemented     |
| Ocean            | searchAssets                       | array[Asset]            | High | x                     | x                         | Not Implemented     |
| Ocean            | searchAssetsByText                 | array[Asset]            | High | x                     | x                         | Not Implemented     |
| Ocean            | searchOrders `tbd`                 | array[Order]            | Low  | Not Implemented       | Not Implemented           | Not Implemented     |
| Ocean            | *getOrdersByAccount                | array[Order]            |      | Not Implemented       | x                         | Not Implemented     |
| Ocean            | registerAsset                      | string                  | High | x                     | x                         | Not Implemented     |
| Ocean            | resolveDID                         | ddo                     | High | x                     | x                         | Not Implemented     |
| Ocean            | *getOrder                          | Order                   | High | Not Implemented       | Not Implemented           | Not Implemented     |
| Ocean            | getAsset                           | Asset                   | High | x                     | x                         | Not Implemented     |
| Ocean            | signServiceAgreement               | hex str (agreement id)  | High | x                     | x                         | Not Implemented     |
| Ocean            | executeServiceAgreement            | hex str (tx. hash)      | High | x                     | x                         | Not Implemented     |
| Ocean            | checkPermissions                   | boolean                 | High | x                     | Not Implemented           | Not Implemented     |
| Ocean            | verifyServiceAgreementSignature    | boolean                 | High | x                     | Not Implemented           | Not Implemented     |
| Ocean            | setMainAccount                     | None                    | High | x                     | Not Implemented           | Not Implemented     |
| Ocean            | consumeService                     | None                    | High | x                     | Not Implemented           | Not Implemented     |
| Account          | getId                              | string                  | High | Not Implemented       | x                         | Not Implemented     |
| Account          | getOceanBalance                    | number/integer          | High | x                     | x                         | Not Implemented     |
| Account          | getEtherBalance                    | number/integer          | High | x                     | x                         | Not Implemented     |
| Account          | getBalance                         | Balance                 | High | x                     | x                         | Not Implemented     |
| Account          | requestTokens                      | number/integer          | High | x                     | x                         | Not Implemented     |
| Account          | unlock                             | None                    | High | x                     | Not Implemented           | Not Implemented     |
| Asset            | getId                              | string                  | High | x                     | x                         | Not Implemented     |
| Asset            | purchase                           | Order                   | High | Not Implemented       | x                         | Not Implemented     |
| Asset            | getDID                             | string                  | High | x                     | Not Implemented           | Not Implemented     |
| Asset            | getDDO                             | ddo                     | High | x                     | Not Implemented           | Not Implemented     |
| Asset            | publishMetadata                    | string                  | High | Not Implemented       | Not Implemented           | Not Implemented     |
| Asset            | getMetadata                        | Metadata                | High | x                     | Not Implemented           | Not Implemented     |
| Asset            | updateMetadata                     | boolean                 | High | Not Implemented       | Not Implemented           | Not Implemented     |
| Asset            | retireMetadata                     | boolean                 | Low  | Not Implemented       | Not Implemented           | Not Implemented     |
| Asset            | getServiceAgreements               | array[ServiceAgreement] | Low  | Not Implemented       | Not Implemented           | Not Implemented     |
| ServiceAgreement | getId                              | string                  | High | Not Implemented       | Not Implemented           | Not Implemented     |
| ServiceAgreement | getPrice                           | number/integer          | High | Not Implemented       | Not Implemented           | Not Implemented     |
| ServiceAgreement | getStatus                          | xxx                     | High | Not Implemented       | Not Implemented           | Not Implemented     |
| ServiceAgreement | publish                            | xxx                     | High | Not Implemented       | Not Implemented           | Not Implemented     |
| ServiceAgreement | retire                             | xxx                     | Low  | Not Implemented       | Not Implemented           | Not Implemented     |
| ServiceAgreement | getAccess                          | xxx                     | High | Not Implemented       | Not Implemented           | Not Implemented     |
| Order            | getId                              | string                  | High | Not Implemented       | x                         | Not Implemented     |
| Order            | getStatus                          | AccessStatus            | High | Not Implemented       | x                         | Not Implemented     |
| Order            | verifyPayment                      | boolean                 | Low  | Not Implemented       | Not Implemented           | Not Implemented     |
| Order            | pay                                | string                  | High | Not Implemented       | x                         | Not Implemented     |
| Order            | commit                             | boolean                 | High | Not Implemented       | x                         | Not Implemented     |
| Order            | consume                            | blob                    | High | Not Implemented       | x                         | Not Implemented     |

Private API

| Class       | Method          | Return Value | Prio  | Python Implementation | Javascript Implementation | Java Implementation |
| :---------- | :-------------- | :----------- | :---- | :-------------------- | :------------------------ | :------------------ |
| SecretStore | encryptDocument | string       | High  | x                     | x                         | x                   |
| SecretStore | decryptDocument | string       | Hight | x                     | x                         | x                   |


## Examples

For examples, see the page listing [Tools & Examples on the Ocean Protocol docs site](https://docs.oceanprotocol.com/concepts/tools/).
