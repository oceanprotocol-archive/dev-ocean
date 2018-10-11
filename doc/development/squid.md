# Squid API

Table of Contents
=================

* [Table of Contents](#table-of-contents)
- [Common](#common)
  * [Getting an instance](#getting-an-instance)
    + [Functions](#functions)
- [Ocean](#ocean)
    + [Functions](#functions-1)
  * [Asset](#asset)
  * [ServiceAgreement](#serviceagreement)
  * [Trader](#trader)
      - [Functions](#functions-2)
  * [Order](#order)
      - [Functions](#functions-3)
  * [Secret Store](#secret-store)
      - [Functions](#functions-4)
- [Squid API Implementation state](#squid-api-implementation-state)
  * [Deleted](#deleted)
    + [Provider Functions (Nice to Have)](#provider-functions--nice-to-have-)
- [Examples](#examples)
   
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
class Ocean {
}

/**
 * Extends OceanBase providing Asset functionalities
*/
class Asset extends OceanBase {}

/**
 * Extends OceanBase providing Order functionalities
*/
class Order extends OceanBase {}

/**
 * Extends OceanBase providing Service Agreement functionalities
*/
class ServiceAgreement extends OceanBase {}

/**
 * Extends OceanBase providing Trader functionalities
*/
class Trader extends OceanBase {}

/**
 * Extends OceanBase providing SecretStore functionalities
*/
class SecretStore extends OceanBase {}


```

### Getting an instance

#### Functions

```
getInstance(web3Dto, providerDto)
```

Parameters:

* web3Dto - Web3 client wrapped in a DTO object to avoid coupling with specific web3 specific libraries
* providerDto - Ocean Provider client wrapped in a DTO object

This method returns an instance of the **Ocean** class. It allows to get access to the other Ocean core methods


In Python 
Instantiate an ocean object


```python
   ocean = Ocean(keeper_url = '', provider_url = '', [web3=web3Object] ...)
```

## Ocean

Interface with core Ocean functions

#### Functions

* **getAccounts** - Returns all avalible accounts loaded via a wallet, or by Web3.
```
array[address, tokenBalance, etherBalance]= ocean.getAccounts()
```

* **getOceanBalance** - SYNC. The only parameter required is an account address (ie. 0x6309b5dd9245278a7fdfb2186dfb80583caeadc7). Returns the Ocean Tokens balance for that account.
```
oceanBalance= ocean.getOceanBalance(address)
```

* **getEtherBalance** - SYNC. The only parameter required is an account address. Returns the Ether balance for that account.
```
ethBalance= ocean.getEtherBalance(address)
```

* **getBalance** - SYNC. The only parameter required is an account address. Returns the Ocean tokens and Ether balance for that account.
```
balance= ocean.getBalance(address)
```

* **requestTokens** - SYNC. Request a number of Ocean Tokens for an address. Returns a boolean to know if everything was right.
```
result= ocean.requestTokens(account_address, amountTokens)
```

* **searchAssets** - SYNC. Given a search query, returns a list of the DDO's matching with that query
```
array[assetDID]= ocean.searchAssets(searchQuery)
```

* **getTrader** - ASYNC. Given an account address create a trader object
```
trader= ocean.getTrader(consumerAddress)
```

* **searchOrders** - SYNC. Return a list of orders by search query. **Nice to Have**.
```
array[orderId]= ocean.searchOrders(searchQuery)
```

* **generateDID** - SYNC. Generates a specific DID with a random id based on the given.

```
did= ocean.generateDID(seed)
```

* **resolveDID** - SYNC. Given a DID the method returns the DDO associated to it.
```
ddo= ocean.resolveDID(did)
```

* **registerAsset** - ASYNC. High-level method publishing the metadata off-chain and registering the Service Agreement on-chain. It orchestrate the `publishAssetMetadata` and `publishServiceAgreement`, and creating a `DDO` methods.
```
Asset= ocean.registerAsset(metadata, services)
```

* **getAsset** - ASYNC. Get an asset based on it's DID or assetId
```
Asset= ocean.getAsset(assetDID | assetId )
```

* **getServiceAgreement** - SYNC. Returns as serviceAgreement object based from the service agreement id (Publisher). **Nice to Have**.
```
serviceAgreement= ocean.getServiceAgreement(serviceAgreementId)
```

* **getOrder** - ASYNC. Get an order based on a orderId
```
Order= ocean.getOrder(orderId)
```


### Asset 

* **getDDO** - ASYNC. Return the created DDO used by this asset.
```
ddo= asset.getDDO()
```

* **getDID** - ASYNC. Return the DID used by this asset.
```
did= asset.getDID()
```
* **getId** - ASYNC. Return the Id used by this asset.
```
id= asset.getId()
```

* **publishMetadata** - SYNC. Given an asset metadata, register this metadata using the assets DDO off-chain. It returns a boolean with the result of the operation.
```
status= asset.publishMetadata(metadata)
```

* **updateMetadata** - SYNC. Given an asset metadata, update the metadata using the asset DDO off-chain. This method replace the complete existing DDO by the DDO provided. It returns a boolean with the result of the operation.
```
status= asset.updateMetadata(metadata)
```

* **getMetadata** - SYNC. returns the metadata stored off chanin by using the asset DDO. Internally calls Ocean.resolveDID(did).
```
metadata= asset.getMetadata()
```

* **getPrice** - SYNC. Given a service agreement id, get's the asset price information existing on-chain.
```
asset.getPrice(serviceAgreementId)
```

* **retireMetadata** - SYNC. Given an Asset metadata, this method delete logically the Asset Metadata. **Nice to Have**
```
asset.retireMetadata(metadata)
```

* **publishServiceAgreement** -  ASYNC. The Publisher register a Service Agreement related with the Asset, returns a ServiceAgreement object
```
ServiceAgreement= asset.publishServiceAgreement(providerId, price, ..)
```

* **getServiceAgreements** - SYNC. Get a list of the service agreements published for this asset. providerId could be optional. **Nice to Have**
```
[serviceAgreementIds]= asset.getServiceAgreements(providerId)
```

### ServiceAgreement

* **getId** - ASYNC. Return the Id used by this serviceAgreement.
```
id= serviceAgreement.getId()
```

* **getStatus** -  SYNC. Return's the status of a Service Agreement. Possible values are (0=> Pending, 1=> Enabled, 2=> Retired)

```
status= serviceAgreement.getStatus()
```

* **retireServiceAgreement** - ASYNC. Given a Service Agreement object, the Publisher retire a Service Agreement. It changes the status to the value 2=>Retired.
```
result= serviceAgreement.retire()
```

* **getAccess** -  SYNC. Get all the information required to get access to an asset after the purchase process
```
access= serviceAgreement.getAccess()
```


### Trader

##### Functions

* **purchaseAsset** - ASYNC. Given an Asset id/did or Asset object and Service Agreement, the Consumer created for the trader class purchases an asset
```
order= trader.purchaseAsset(Asset or assetId or assetDID, serviceAgreementId or ServiceAgreement, timeout)
```


### Order

##### Functions

* **getId** - ASYNC. Return the Id used by this order.
```
id= order.getId()
```

* **getStatus** - SYNC. Using an order object, returns an integer representing the order status as defined in the keeper. Possible values are (0=>Pending, 1=>Paid, 2=>Canceled)
```
status= order.getStatus()
```

* **verifyPayment** -  SYNC. Given an order object, verifies if the order was paid.
```
status= order.verifyPayment()
```

* **getAccess** -  SYNC. Get all the information required to get access to an asset after the purchase process, using the internal assetI and serviceAgreementId
```
access= order.getAccess()
```

### Secret Store

##### Functions

* **encryptDocument** - SYNC. **Private function** encapsulated as part of the **register** function. It integrates the Parity Ethereum & Secret Store API allowing to encrypt a document.
Given by a **Publisher** an unique resource id (did), the document to encrypt and the Secret Store cluster threshold (could be pre-defined to a fixed number), integrate the Secret Store API's to encrypt the document.
```
encryptedDocument= encryptDocument(did, document, threshold)
```

* **decryptDocument** - SYNC.  **Private function** encapsulated as part of the **getAssetAccess** function. It integrates the Parity Ethereum & Secret Store API allowing to decrypt a document if the user executing this function is authorized by a Smart Contract.
Given by a **Consumer** an unique resource id (did) and the document encrypted, this functions integrates the Secret Store API's to decrypt the document. The on-chain authorization phase is transparent for the user.
```
document= decryptDocument(did, encryptedDocument)
```


## Squid API Implementation state

Table not completed yet

| Category                  | Method                      | Python Implementation   | Javascript Implementation   | Java Implementation   |
|:--------------------------|:----------------------------|-------------------------|-----------------------------|-----------------------|
| Ocean                     | getInstance                 | Not Implemented         | x                           | Not Implemented       |
| Ocean                     | Ocean                       | Not Implemented         | x                           | Not Implemented       |
| Ocean                     | getAccounts                 | Not Implemented         | x                           | Not Implemented       |
| Ocean                     | getOceanBalance             | Not Implemented         | x                           | Not Implemented       |
| Ocean                     | getEtherBalance             | Not Implemented         | x                           | Not Implemented       |
| Ocean                     | getBalance                  | Not Implemented         | x                           | Not Implemented       |
| Ocean                     | searchAssets                | Not Implemented         | Not Implemented             | Not Implemented       |
| Ocean                     | getTrader                   | Not Implemented         | Not Implemented             | Not Implemented       |
| Ocean                     | searchOrders                | Not Implemented         | Not Implemented             | Not Implemented       |
| Ocean                     | requestTokens               | Not Implemented         | x                           | Not Implemented       |
| Ocean                     | register                    | Not Implemented         | Not Implemented             | Not Implemented       |
| Ocean                     | getTrader                   | Not Implemented         | Not Implemented             | Not Implemented       |
| Ocean                     | generateDID                 | Not Implemented         | Not Implemented             | Not Implemented       |
| Ocean                     | resolveDID                  | Not Implemented         | Not Implemented             | Not Implemented       |
| Ocean                     | registerAsset               | Not Implemented         | Not Implemented             | Not Implemented       |
| Ocean                     | getServiceAggremments       | Not Implemented         | Not Implemented             | Not Implemented       |
| Ocean                     | getOrder                    | Not Implemented         | Not Implemented             | Not Implemented       |
| Asset                     | getDDO                      | Not Implemented         | Not Implemented             | Not Implemented       |
| Asset                     | getDID                      | Not Implemented         | Not Implemented             | Not Implemented       |
| Asset                     | getId                       | Not Implemented         | Not Implemented             | Not Implemented       |
| Asset                     | publishMetadata             | Not Implemented         | Not Implemented             | Not Implemented       |
| Asset                     | updateMetadata              | Not Implemented         | Not Implemented             | Not Implemented       |
| Asset                     | getMetadata                 | Not Implemented         | Not Implemented             | Not Implemented       |
| Asset                     | getPrice                    | Not Implemented         | Not Implemented             | Not Implemented       |
| Asset                     | retireMetadata              | Not Implemented         | Not Implemented             | Not Implemented       |
| Asset                     | publishServiceAgreement     | Not Implemented         | Not Implemented             | Not Implemented       |
| Asset                     | getServiceAgreements        | Not Implemented         | Not Implemented             | Not Implemented       |
| ServiceAgreement          | getId                       | Not Implemented         | Not Implemented             | Not Implemented       |
| ServiceAgreement          | getStatus                   | Not Implemented         | Not Implemented             | Not Implemented       |
| ServiceAgreement          | retire                      | Not Implemented         | Not Implemented             | Not Implemented       |
| ServiceAgreement          | getAccess                   | Not Implemented         | Not Implemented             | Not Implemented       |
| Trader                    | purchaseAsset               | Not Implemented         | Not Implemented             | Not Implemented       |
| Order                     | getId                       | Not Implemented         | Not Implemented             | Not Implemented       |
| Order                     | getStatus                   | Not Implemented         | Not Implemented             | Not Implemented       |
| Order                     | verifyPayment               | Not Implemented         | Not Implemented             | Not Implemented       |
| Order                     | getAccess                   | Not Implemented         | Not Implemented             | Not Implemented       |
| Ocean.SecretStore         | encryptDocument             | Not Implemented         | Not Implemented             | Not Implemented       |
| Ocean.SecretStore         | decryptDocument             | Not Implemented         | Not Implemented             | Not Implemented       |

### Deleted

- Ocean(config(web3Provider, nodeURI, gas, network, providerURI))

Constructor is private so only way to get an instance is using the `getInstance()` method.

#### Provider Functions (Nice to Have)

* **registerProvider** -
```
result= registerProvider(providerAddress, url)
```

* **getProviders** -
```
getProviders()
```

* **getAssetProviders** - Get the Providers giving access to an Asset
```
getAssetProviders(assetDID)
```

## Examples

List orders in JavaScript

```javascript
import { Ocean } from '@oceanprotocol/squid'

const ocean = await Ocean.getInstance({
    nodeUri: 'http://localhost:8545'
})

const orders = await ocean.order.getOrdersByConsumer('0x970e8f18ebfEa0B08810f33a5A40438b9530FBCF')

console.log('Orders', JSON.stringify(orders, null, 2))
```

List accounts in JavaScript
```javascript
import { Ocean } from '@oceanprotocol/squid'

const ocean = await Ocean.getInstance({
    nodeUri: 'http://localhost:8545'
})

const accounts = await ocean.accounts()

console.log(JSON.stringify(accounts, null, 2))
```

Access market functionality in JavaScript

```javascript
import { Ocean } from '@oceanprotocol/squid'

const ocean = await Ocean.getInstance({
    nodeUri: 'http://localhost:8545'
})
const { orders } = ocean

await orders.verifyOrderPayment('0x970e8f18ebfEa0B08810f33a5A40438b9530FBCF')
```
