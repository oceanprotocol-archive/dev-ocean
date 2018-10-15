# Squid API

Table of Contents
=================

* [Table of Contents](#table-of-contents)
* [Common](#common)
  * [Getting an instance](#getting-an-instance)
    * [Functions](#functions)
* [Ocean](#ocean)
  * [Functions](#functions-1)
  * [Asset](#asset)
     * [Functions](#functions-2)
  * [ServiceAgreement](#serviceagreement)
      * [Functions](#functions-3)
  * [Trader](#trader)
      - [Functions](#functions-4)
  * [Order](#order)
      - [Functions](#functions-5)
  * [Secret Store](#secret-store)
      - [Functions](#functions-6)
* [Squid API Implementation state](#squid-api-implementation-state)
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
    public Order
    public Asset
    public ServiceAgreement
    public Trader    
    public SecretStore
}

/**
 * Extends OceanBase providing Order functionalities
*/
class Order extends OceanBase {}

/**
 * Extends OceanBase providing Asset functionalities
*/
class Asset extends OceanBase {}

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

In JavaScript

```javascript
import { Ocean } from '@oceanprotocol/squid'

const ocean = await Ocean.getInstance({...})
```

## Ocean

Interface with core Ocean functions

#### Functions

* **getAccounts** - Returns all avalible accounts loaded via a wallet, or by Web3.
```
array[address]= ocean.getAccounts()
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
array[asset]= ocean.searchAssets(searchQuery)
```

* **searchOrders** - SYNC. Return a list of orders by search query. **Nice to Have**.
```
array[order]= ocean.searchOrders(searchQuery)
```

* **generateDID** - SYNC. Generates a specific DID with a random id based on the given.
```
did= ocean.generateDID(seed)
```

* **resolveDID** - SYNC. Given a DID the method returns the DDO associated to it.
```
ddo= ocean.resolveDID(did)
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

Interface provides access to asset functions

#### Functions

* **getDDO** - SYNC. Return the created DDO used by this asset.
```
ddo= ocean.asset.getDDO()
```

* **getDID** - SYNC. Return the DID used by this asset.
```
did= ocean.asset.getDID()
```

* **getId** - SYNC. Return the Id used by this asset.
```
id= ocean.asset.getId()
```

* **registerAsset** - ASYNC. High-level method publishing the metadata off-chain and registering the Service Agreement on-chain. It orchestrate the `publishAssetMetadata` and `publishServiceAgreement`, and creating a `DDO` methods.
```
Asset= ocean.asset.registerAsset(metadata, services)
```

* **publishMetadata** - SYNC. Given an asset metadata, register this metadata using the assets DDO off-chain. It returns a boolean with the result of the operation.
```
status= ocean.asset.publishMetadata(metadata)
```

* **updateMetadata** - SYNC. Given an asset metadata, update the metadata using the asset DDO off-chain. This method replace the complete existing DDO by the DDO provided. It returns a boolean with the result of the operation.
```
status= ocean.asset.updateMetadata(metadata)
```

* **getMetadata** - SYNC. returns the metadata stored off chanin by using the asset DDO. Internally calls Ocean.resolveDID(did).
```
metadata= ocean.asset.getMetadata()
```

* **getPrice** - SYNC. Given a service agreement id, get's the asset price information existing on-chain.
```
price= ocean.asset.getPrice(serviceAgreementId)
```

* **retireMetadata** - SYNC. Given an Asset metadata, this method delete logically the Asset Metadata. **Nice to Have**
```
ocean.asset.retireMetadata(metadata)
```

* **publishServiceAgreement** -  ASYNC. The Publisher register a Service Agreement related with the Asset, returns a ServiceAgreement object
```
serviceAgreement= ocean.asset.publishServiceAgreement(providerId, price, ..)
```

* **getServiceAgreements** - SYNC. Get a list of the service agreements published for this asset. providerId could be optional. **Nice to Have**
```
[serviceAgreementIds]= ocean.asset.getServiceAgreements(providerId)
```

### ServiceAgreement

Interface provides access to ServiceAgreement functions

#### Functions

* **getId** - SYNC. Return the Id used by this serviceAgreement.
```
id= ocean.serviceAgreement.getId()
```

* **getStatus** -  SYNC. Return's the status of a Service Agreement. Possible values are (0=> Pending, 1=> Enabled, 2=> Retired)

```
status= ocean.serviceAgreement.getStatus()
```

* **retireServiceAgreement** - ASYNC. Given a Service Agreement object, the Publisher retire a Service Agreement. It changes the status to the value 2=>Retired.
```
result= ocean.serviceAgreement.retire()
```

* **getAccess** -  SYNC. Get all the information required to get access to an asset after the purchase process
```
access= ocean.serviceAgreement.getAccess()
```

### Trader

Interface provides access to Trader functions

##### Functions

* **purchaseAsset** - ASYNC. Given an Asset id/did or Asset object and Service Agreement, the Consumer created for the trader class purchases an asset
```
order= ocean.trader.purchaseAsset(Asset or assetId or assetDID, serviceAgreementId or ServiceAgreement, timeout)
```

### Order

Interface provides access to Order functions

##### Functions

* **getId** - SYNC. Return the Id used by this order.
```
id= ocean.order.getId()
```

* **getStatus** - SYNC. Using an order object, returns an integer representing the order status as defined in the keeper. Possible values are (0=>Pending, 1=>Paid, 2=>Canceled)
```
status= ocean.order.getStatus()
```

* **verifyPayment** -  SYNC. Given an order object, verifies if the order was paid.
```
status= ocean.order.verifyPayment()
```

* **getAccess** -  SYNC. Get all the information required to get access to an asset after the purchase process, using the internal assetI and serviceAgreementId
```
access= ocean.order.getAccess()
```

#### SecretStore

Interface provides access to SecretStore functions

##### Functions

* **encryptDocument** - SYNC. **Private function** encapsulated as part of the **register** function. It integrates the Parity Ethereum & Secret Store API allowing to encrypt a document.
Given by a **Publisher** an unique resource id (did), the document to encrypt and the Secret Store cluster threshold (could be pre-defined to a fixed number), integrate the Secret Store API's to encrypt the document.
```
encryptedDocument= ocean.secretStore.encryptDocument(did, document, threshold)
```

* **decryptDocument** - SYNC.  **Private function** encapsulated as part of the **getAssetAccess** function. It integrates the Parity Ethereum & Secret Store API allowing to decrypt a document if the user executing this function is authorized by a Smart Contract.
Given by a **Consumer** an unique resource id (did) and the document encrypted, this functions integrates the Secret Store API's to decrypt the document. The on-chain authorization phase is transparent for the user.
```
document= ocean.secretStore.decryptDocument(did, encryptedDocument)
```

## Squid API Implementation state

Table not completed yet

| Category                  | Method                      | Python Implementation   | Javascript Implementation   | Java Implementation   |
|:--------------------------|:----------------------------|-------------------------|-----------------------------|-----------------------|
| Ocean                     | 1 getInstance                 | Not Implemented         | x                           | Not Implemented       |
| Ocean                     | 1 Ocean                       | Not Implemented         | x                           | Not Implemented       |
| Ocean                     | 1 getAccounts                 | Not Implemented         | x                           | Not Implemented       |
| Ocean                     | 1 getOceanBalance             | Not Implemented         | x                           | Not Implemented       |
| Ocean                     | 1 getEtherBalance             | Not Implemented         | x                           | Not Implemented       |
| Ocean                     | 1 getBalance                  | Not Implemented         | x                           | Not Implemented       |
| Ocean                     | 1 searchAssets                | Not Implemented         | Not Implemented             | Not Implemented       |
| Ocean                     | 2 getTrader                   | Not Implemented         | Not Implemented             | Not Implemented       |
| Ocean                     | 2 searchOrders                | Not Implemented         | Not Implemented             | Not Implemented       |
| Ocean                     | 1 requestTokens               | Not Implemented         | x                           | Not Implemented       |
| Ocean                     | 1 register                    | Not Implemented         | Not Implemented             | Not Implemented       |
| Ocean                     | 1 generateDID                 | Not Implemented         | Not Implemented             | Not Implemented       |
| Ocean                     | 1 resolveDID                  | Not Implemented         | Not Implemented             | Not Implemented       |
| Ocean                     | 1 getServiceAggremments       | Not Implemented         | Not Implemented             | Not Implemented       |
| Ocean                     | 1 getOrder                    | Not Implemented         | Not Implemented             | Not Implemented       |
| Asset                     | 1 registerAsset               | Not Implemented         | Not Implemented             | Not Implemented       |
| Asset                     | 1 getId                       | Not Implemented         | Not Implemented             | Not Implemented       |
| Asset                     | 1 getDDO                      | Not Implemented         | Not Implemented             | Not Implemented       |
| Asset                     | 1 getDID                      | Not Implemented         | Not Implemented             | Not Implemented       |
| Asset                     | 1 publishMetadata             | Not Implemented         | Not Implemented             | Not Implemented       |
| Asset                     | 1 updateMetadata              | Not Implemented         | Not Implemented             | Not Implemented       |
| Asset                     | 1 getMetadata                 | Not Implemented         | Not Implemented             | Not Implemented       |
| Asset                     | 1 getPrice                    | Not Implemented         | Not Implemented             | Not Implemented       |
| Asset                     | 2 retireMetadata              | Not Implemented         | Not Implemented             | Not Implemented       |
| Asset                     | 1 publishServiceAgreement     | Not Implemented         | Not Implemented             | Not Implemented       |
| Asset                     | 2 getServiceAgreements        | Not Implemented         | Not Implemented             | Not Implemented       |
| ServiceAgreement          | 1 getId                       | Not Implemented         | Not Implemented             | Not Implemented       |
| ServiceAgreement          | 1 getStatus                   | Not Implemented         | Not Implemented             | Not Implemented       |
| ServiceAgreement          | 2 retire                      | Not Implemented         | Not Implemented             | Not Implemented       |
| ServiceAgreement          | 1 getAccess                   | Not Implemented         | Not Implemented             | Not Implemented       |
| Trader                    | 1 purchaseAsset               | Not Implemented         | Not Implemented             | Not Implemented       |
| Order                     | 1 getId                       | Not Implemented         | Not Implemented             | Not Implemented       |
| Order                     | 1 getStatus                   | Not Implemented         | Not Implemented             | Not Implemented       |
| Order                     | 2 verifyPayment               | Not Implemented         | Not Implemented             | Not Implemented       |
| Order                     | 1 getAccess                   | Not Implemented         | Not Implemented             | Not Implemented       |
| SecretStore               | 1 encryptDocument             | Not Implemented         | Not Implemented             | Not Implemented       |
| SecretStore               | 1 decryptDocument             | Not Implemented         | Not Implemented             | Not Implemented       |

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
