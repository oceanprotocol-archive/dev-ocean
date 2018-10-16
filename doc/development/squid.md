# Squid API

Table of Contents
=================

* [Common](#common)
  * [Getting an instance](#getting-an-instance)
    * [Functions](#functions)
* [Ocean](#ocean)
  * [Functions](#functions-1)
  * [Account](#account)
     - [Functions](#functions-2)
  * [Asset](#asset)
     - [Functions](#functions-3)
  * [ServiceAgreement](#serviceagreement)
      - [Functions](#functions-4)
  * [Trader](#trader)
      - [Functions](#functions-5)
  * [Order](#order)
      - [Functions](#functions-6)
  * [Secret Store](#secret-store)
      - [Functions](#functions-7)
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
ocean = Ocean(web3Dto, providerDto)

# for comaptability use get_instance to return an Ocean object
ocean = Ocean.get_instance(web3Dto, providerDto)


class Ocean:
  ...
  @staticmethod
  def getInsance(web3Dto, providerDto):
    return Ocean(web3Dto, providerDto)
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

* **searchAssets** - SYNC. Given a search query, returns a list of the DDO's matching with that query
```
array[asset]= ocean.searchAssets(searchQuery)
```
You have to do a request to this endpoint:
POST {provider.url}/api/v1/provider/assets/metadata/query
This method is expecting a json object that contains the following structure:
 ```  {
        "offset": 100,
        "page": 0,
        "query": {
          "value": 1
        },
        "sort": {
          "value": 1
        },
        "text": "Office"
}
```
The only mandatory argument is query or text, but you have to use only one of them. The other fields are optionals.

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

### Account

Ocean Account object

#### Functions

* **getOceanBalance** - SYNC. Returns the Ocean Tokens balance for that account.
```
oceanBalance= account.getOceanBalance()
```

* **getEtherBalance** - SYNC. Returns the Ether balance for that account.
```
ethBalance= account.getEtherBalance()
```

* **getBalance** - SYNC. Returns an instance of the Balance object for that account.
```
balance= account.getBalance()
```

* **requestTokens** - ASYNC. Request a number of Ocean Tokens. Returns the number of tokens accounted.
```
amount= account.requestTokens(amountTokens)
```

### Asset

Interface provides access to asset functions

#### Functions

* **getId** - SYNC. Return the Id used by this asset.
```
id= asset.getId()
```

* **getDDO** - SYNC. Return the created DDO used by this asset.
```
ddo= ocean.asset.getDDO()
```

* **getDID** - SYNC. Return the DID used by this asset.
```
did= ocean.asset.getDID()
```

* **register** - ASYNC. High-level method publishing the metadata off-chain and registering the Service Agreement on-chain. It orchestrate the `publishAssetMetadata` and `publishServiceAgreement`, and creating a `DDO` methods.
```
Asset= ocean.asset.register(metadata, services)
```

Functions beeing called:
    * publishMetadata
    * publishServiceAgreement (from ServiceAgreement)
    * encryptDocument (from SecretStore)

* **publishMetadata** - SYNC. Given an asset metadata, register this metadata using the assets DDO off-chain. It returns a boolean with the result of the operation.
```
status= ocean.asset.publishMetadata(metadata)
```
You have to do a request to this endpoint:
POST {provider.url}/api/v1/provider/assets/metadata
This method is expecting a json object that contains the following structure:
 ```   {"assetId": ,
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

In the base object there are more optional fields that you can check in the [provider API](https://github.com/oceanprotocol/provider/blob/develop/provider/app/assets.py).

* **updateMetadata** - SYNC. Given an asset metadata, update the metadata using the asset DDO off-chain. This method replace the complete existing DDO by the DDO provided. It returns a boolean with the result of the operation.
```
status= ocean.asset.updateMetadata(metadata)
```
You have to do a request to this endpoint:
PUT {provider.url}/api/v1/provider/assets/metadata
This method is expecting a json object that contains the following structure:
 ```   {"assetId": ,
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
            "rating":
            "numVotes":
        }
       }
```
In the base object there are more optional fields that you can check in the [provider API](https://github.com/oceanprotocol/provider/blob/develop/provider/app/assets.py).

* **getMetadata** - SYNC. returns the metadata stored off chanin by using the asset DDO. Internally calls Ocean.resolveDID(did).
```
metadata= ocean.asset.getMetadata()
```
You have to do a request to this endpoint:
GET {provider.url}/api/v1/provider/assets/metadata/<asset_id>

* **getPrice** - SYNC. Given a service agreement id, get's the asset price information existing on-chain.
```
price= ocean.asset.getPrice(serviceAgreementId)
```

`tbd`

* **retireMetadata** - ASYNC. Given an Asset metadata, this method delete logically the Asset Metadata. **Nice to Have**
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

* **retire** - ASYNC. Given a Service Agreement object, the Publisher retire a Service Agreement. It changes the status to the value 2=>Retired.
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
id= order.getId()
```

* **getStatus** - SYNC. Using an order object, returns an integer representing the order status as defined in the keeper. It is described as an Enum Possible values are (0=>Pending, 1=>Paid, 2=>Canceled)
```
status= order.getStatus()
```

* **verifyPayment** -  SYNC. Given an order object, verifies if the order was paid.
```
status= order.verifyPayment()
```

* **consume** -  SYNC. Get all the information required to get access to an asset after the purchase process, using the internal assetId and serviceAgreementId
```
url= ocean.consume()
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

Public API

| Class                     | Method                             | Return Value            | Prio   | Python Implementation   | Javascript Implementation   | Java Implementation   |
|:--------------------------|:-----------------------------------|:------------------------|:-------|:------------------------|:----------------------------|:----------------------|
| Ocean                     | getInstance (js, java)/ Ocean (py) | Ocean                   | High   | Not Implemented         | x                           | Not Implemented       |
| Ocean                     | getAccounts                        | array[Account]          | High   | Not Implemented         | x                           | Not Implemented       |
| Ocean                     | searchAssets                       | array[Asset]            | High   | Not Implemented         | Not Implemented             | Not Implemented       |
| Ocean                     | searchOrders `tbd`                 | array[Order]            | Low    | Not Implemented         | Not Implemented             | Not Implemented       |
| Ocean                     | register                           | Asset                   | High   | Not Implemented         | Not Implemented             | Not Implemented       |
| Ocean                     | generateDID                        | string                  | High   | Not Implemented         | Not Implemented             | Not Implemented       |
| Ocean                     | resolveDID                         | ddo                     | High   | Not Implemented         | Not Implemented             | Not Implemented       |
| Ocean                     | getOrder                           | Order                   | High   | Not Implemented         | Not Implemented             | Not Implemented       |
| Ocean                     | getAsset                           | Asset                   | High   | Not Implemented         | Not Implemented             | Not Implemented       |
| Account                   | getOceanBalance                    | number/integer          | High   | Not Implemented         | x                           | Not Implemented       |
| Account                   | getEtherBalance                    | number/integer          | High   | Not Implemented         | x                           | Not Implemented       |
| Account                   | getBalance                         | Balance                 | High   | Not Implemented         | x                           | Not Implemented       |
| Account                   | requestTokens                      | number/integer          | High   | Not Implemented         | x                           | Not Implemented       |
| Asset                     | purchase                           | Order                   | High   | Not Implemented         | Not Implemented             | Not Implemented       |
| Asset                     | getId                              | string                  | High   | Not Implemented         | Not Implemented             | Not Implemented       |
| Asset                     | getDDO                             | ddo                     | High   | Not Implemented         | Not Implemented             | Not Implemented       |
| Asset                     | getDID                             | string                  | High   | Not Implemented         | Not Implemented             | Not Implemented       |
| Asset                     | publishMetadata                    | string                  | High   | Not Implemented         | Not Implemented             | Not Implemented       |
| Asset                     | getMetadata                        | Metadata                | High   | Not Implemented         | Not Implemented             | Not Implemented       |
| Asset                     | updateMetadata                     | boolean                 | High   | Not Implemented         | Not Implemented             | Not Implemented       |
| Asset                     | updateMetadata                     | boolean                 | High   | Not Implemented         | Not Implemented             | Not Implemented       |
| Asset                     | retireMetadata                     | boolean                 | Low    | Not Implemented         | Not Implemented             | Not Implemented       |
| Asset                     | getServiceAgreements               | array[ServiceAgreement] | Low    | Not Implemented         | Not Implemented             | Not Implemented       |
| ServiceAgreement          | getId                              | string                  | High   | Not Implemented         | Not Implemented             | Not Implemented       |
| ServiceAgreement          | getPrice                           | number/integer          | High   | Not Implemented         | Not Implemented             | Not Implemented       |
| ServiceAgreement          | getStatus                          | xxx                     | High   | Not Implemented         | Not Implemented             | Not Implemented       |
| ServiceAgreement          | publish                            | xxx                     | High   | Not Implemented         | Not Implemented             | Not Implemented       |
| ServiceAgreement          | retire                             | xxx                     | Low    | Not Implemented         | Not Implemented             | Not Implemented       |
| ServiceAgreement          | getAccess                          | xxx                     | High   | Not Implemented         | Not Implemented             | Not Implemented       |
| Order                     | getId                              | string                  | High   | Not Implemented         | Not Implemented             | Not Implemented       |
| Order                     | getStatus                          | Status                  | High   | Not Implemented         | Not Implemented             | Not Implemented       |
| Order                     | verifyPayment                      | boolean                 | Low    | Not Implemented         | Not Implemented             | Not Implemented       |
| Order                     | consume                            | blob                    | High   | Not Implemented         | Not Implemented             | Not Implemented       |

Private API                                                                                 

| Class                     | Method                             | Return Value            | Prio   | Python Implementation   | Javascript Implementation   | Java Implementation   |
|:--------------------------|:-----------------------------------|:------------------------|:-------|:------------------------|:----------------------------|:----------------------|
| SecretStore               | encryptDocument                    | string                  | High   | Not Implemented         | Not Implemented             | Not Implemented       |
| SecretStore               | decryptDocument                    | string                  | Hight  | Not Implemented         | Not Implemented             | Not Implemented       |

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
