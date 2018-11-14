# Squid API

The Squid API is a Level-2 API built on top of core Ocean components. It's a facilitator or enabler, but it's not the only way to interact with Ocean.

The goal of this doc is to help a developer develop a version of the Squid API in any programming language. Currently, the Squid API is defined for Object-Oriented languages such as JavaScript, Java, and Python (which are the three initial implementation languages).

<!--ts-->
   * [Squid API](#squid-api)
      * [Common](#common)
         * [Getting an instance](#getting-an-instance)
            * [Methods](#methods)
      * [Ocean](#ocean)
         * [Methods](#methods-1)
      * [Account](#account)
         * [Methods](#methods-2)
      * [Asset](#asset)
         * [Methods](#methods-3)
      * [ServiceAgreement](#serviceagreement)
         * [Methods](#methods-4)
      * [Order](#order)
         * [Methods](#methods-5)
      * [SecretStore (Private)](#secretstore-private)
         * [Methods](#methods-6)
      * [DID-DDO-Library (Private)](#did-ddo-library-private)
      * [Squid API Implementation state](#squid-api-implementation-state)
      * [Examples](#examples)

<!-- Added by: batman, at: 2018-10-18T09:08+02:00 -->

<!--te-->

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

#### Methods

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
  def getInstance(web3Dto, providerDto):
    return Ocean(web3Dto, providerDto)
```

In JavaScript

```javascript
import { Ocean } from '@oceanprotocol/squid'

const ocean = await Ocean.getInstance({...})
```

## Ocean

This class serves as the interface with Ocean Protocol. The Ocean class aggregates the list of **Account**s (ethereum accounts), list of **Asset**s, and a list of **Order** objects. The Ocean class aggregates the Keeper class, which in turn interfaces with the running Smart Contracts: Market, Token, and Auth. 

### Methods

* **getAccounts** - ASYNC. Returns all available accounts loaded via a wallet, or by Web3.
```
array[Account] = ocean.getAccounts()
```

* **searchAssets** - ASYNC. Given a search query, returns a list of the Asset objects matching with that query. 
```
array[Asset] = ocean.searchAssets(searchQuery)
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
 
 * **searchAssetsByText** - ASYNC. Given a search query, returns a list of the Asset objects matching with that query. 
 ```
 array[Asset] = ocean.searchAssetsByText("office")
 ```

The only mandatory argument is query or text, but you have to use only one of them. The other fields are optional.

* **searchOrders** - (*TBD*) SYNC. Return a list of orders by search query. **Nice to Have**.
```
array[Order] = ocean.searchOrders(searchQuery)
```

* **getOrdersByAccount** - ASYNC. Returns a list of orders by given account.
```
array[Order] = ocean.getOrdersByAccount(account)
```

* **register** - ASYNC. High-level method publishing the metadata off-chain and registering the Service Agreement on-chain. It orchestrate the publishAssetMetadata and publishServiceAgreement, and creating a DDO methods.
```
asset_did = ocean.register(asset)
```

* **resolveDID** - SYNC. Given a DID, return the associated DID Document (DDO). The DDO is resolved by directly interacting with the keeper node. 
```
DDO = ocean.resolveDID(did)
```

- **getOrder** - ASYNC. Get an order from the given orderId.

```
Order = ocean.getOrder(orderId)
```

* **getAsset** - ASYNC. Get an asset based on its identifier, in the form of a DID. 

```
Asset = ocean.getAsset(asset_DID)
```

- **getServiceAgreement** - SYNC. Returns as serviceAgreement object based from the service agreement id (Publisher). **Nice to Have**.

```
serviceAgreement = ocean.getServiceAgreement(serviceAgreementId)
```

## Account

Ocean Account object

### Methods

* **getId** - SYNC. Return the Id used by this account.
```
id= account.getId()
```

* **getOceanBalance** - ASYNC. Returns the Ocean Tokens balance for that account.
```
oceanBalance= account.getOceanBalance()
```

* **getEtherBalance** - ASYNC. Returns the Ether balance for that account.
```
ethBalance= account.getEtherBalance()
```

* **getBalance** - ASYNC. Returns an instance of the Balance object for that account.
```
balance= account.getBalance()
```

* **requestTokens** - ASYNC. Request a number of Ocean Tokens. Returns the number of tokens accounted.
```
amount= account.requestTokens(amountTokens)
```

## Asset

Interface provides access to assets. 

### Methods

* **getId** - SYNC. Return the Id used by this asset.
```
id= order.getId()
```

* **purchase** - ASYNC. Given an Asset id/did or Asset object and Service Agreement, the Consumer created for the  class purchases an asset
```
order = asset.purchase(assetDID, serviceAgreementId, timeout)
```

* **getDDO** - SYNC. Return the created DDO used by this asset.
```
ddo = asset.getDDO()
```

- **getDID** - SYNC. Return the DID used by this asset.
```
did = asset.getDID()
```

* **publishMetadata** - SYNC. Given an asset metadata, register this metadata using the assets DDO off-chain. It returns a boolean with the result of the operation.
```
status= asset.publishMetadata(metadata)
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

- **getMetadata** - SYNC. returns the metadata stored off chanin by using the asset DDO. Internally calls Ocean.resolveDID(did).

```
metadata= asset.getMetadata()
```

You have to do a request to this endpoint:
GET {provider.url}/api/v1/provider/assets/metadata/<asset_id>

* **updateMetadata** - SYNC. Given an asset metadata, update the metadata using the asset DDO off-chain. This method replace the complete existing DDO by the DDO provided. It returns a boolean with the result of the operation.
```
status= asset.updateMetadata(metadata)
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

* **retireMetadata** - ASYNC. Given an Asset metadata, this method delete logically the Asset Metadata. **Nice to Have**
```
asset.retireMetadata(metadata)
```

* **getServiceAgreements** - SYNC. Get a list of the service agreements published for this asset. providerId could be optional. **Nice to Have**
```
[serviceAgreementIds] = asset.getServiceAgreements(providerId)
```

## ServiceAgreement

Interface provides access to ServiceAgreement functions

### Methods

* **getId** - SYNC. Return the Id used by this serviceAgreement.
```
id = serviceAgreement.getId()
```

- **getPrice** - SYNC. Given a service agreement id, get's the asset price information existing on-chain.

```
price = serviceAgreement.getPrice(serviceAgreementId)
tbd
```

* **getStatus** - SYNC. Return's the status of a Service Agreement. Possible values are (0=> Pending, 1=> Enabled, 2=> Retired)

```
status = serviceAgreement.getStatus()
```

* **publish** - ASYNC. The Publisher register a Service Agreement related with the Asset, returns a ServiceAgreement object

```
serviceAgreement = serviceAgreement.publish(providerId, price, ..)
```

* **retire** - ASYNC. Given a Service Agreement object, the Publisher retire a Service Agreement. It changes the status to the value 2=>Retired.
```
result = serviceAgreement.retire()
```

* **getAccess** - SYNC. Get all the information required to get access to an asset after the purchase process
```
access = serviceAgreement.getAccess()
```

## Order

Interface provides access to Order functions

### Methods

* **getId** - SYNC. Return the Id used by this order.
```
id= order.getId()
```

* **getStatus** - ASYNC. Using an order object, returns an integer representing the order status as defined in the keeper. It is described as an Enum Possible values are (0=>Pending, 1=>Paid, 2=>Canceled)
```
status= order.getStatus()
```

* **commit** - ASYNC. Publisher commits this order with the access token.
```
isCommited= order.commit(accessToken)
```

* **pay** - ASYNC. Pay for this order with given account.
```
paymentId= order.pay(consumerAccount)
```

* **verifyPayment** - SYNC. Given an order object, verifies if the order was paid.
```
status= order.verifyPayment()
```

* **consume** - ASYNC. Get all the information required to get access to an asset after the purchase process, using the internal assetId and serviceAgreementId
```
url= order.consume(consumerAccount)
```

## SecretStore (Private)

Interface provides access to SecretStore functions

### Methods

* **encryptDocument** - SYNC. **Private function** encapsulated as part of the **register** function. It integrates the Parity Ethereum & Secret Store API allowing to encrypt a document.
Given by a **Publisher** an unique resource id (did), the document to encrypt and the Secret Store cluster threshold (could be pre-defined to a fixed number), integrate the Secret Store API's to encrypt the document.
```
encryptedDocument= secretStore.encryptDocument(did, document, threshold)
```

* **decryptDocument** - SYNC.  **Private function** encapsulated as part of the **getAssetAccess** function. It integrates the Parity Ethereum & Secret Store API allowing to decrypt a document if the user executing this function is authorized by a Smart Contract.
Given by a **Consumer** an unique resource id (did) and the document encrypted, this functions integrates the Secret Store API's to decrypt the document. The on-chain authorization phase is transparent for the user.
```
document= secretStore.decryptDocument(did, encryptedDocument)
```

## DID-DDO-Library (Private)

TBD

## Squid API Implementation state

Public API

| Class            | Method                             | Return Value            | Prio | Python Implementation | Javascript Implementation | Java Implementation |
| :--------------- | :--------------------------------- | :---------------------- | :--- | :-------------------- | :------------------------ | :------------------ |
| Ocean            | getInstance (js, java)/ Ocean (py) | Ocean                   | High | Not Implemented       | x                         | Not Implemented     |
| Ocean            | getAccounts                        | array[Account]          | High | x                     | x                         | Not Implemented     |
| Ocean            | searchAssets                       | array[Asset]            | High | Not Implemented       | Not Implemented           | Not Implemented     |
| Ocean            | searchOrders `tbd`                 | array[Order]            | Low  | Not Implemented       | Not Implemented           | Not Implemented     |
| Ocean            | getOrdersByAccount                 | array[Order]            |      | Not Implemented       | Not Implemented           | Not Implemented     |
| Ocean            | register                           | string                  | High | Not Implemented       | x                         | Not Implemented     |
| Ocean            | resolveDID                         | ddo                     | High | Not Implemented       | Not Implemented           | Not Implemented     |
| Ocean            | getOrder                           | Order                   | High | Not Implemented       | Not Implemented           | Not Implemented     |
| Ocean            | getAsset                           | Asset                   | High | Not Implemented       | Not Implemented           | Not Implemented     |
| Account          | getId                              | string                  | High | Not Implemented       | x                         | Not Implemented     |
| Account          | getOceanBalance                    | number/integer          | High | x                     | x                         | Not Implemented     |
| Account          | getEtherBalance                    | number/integer          | High | x                     | x                         | Not Implemented     |
| Account          | getBalance                         | Balance                 | High | x                     | x                         | Not Implemented     |
| Account          | requestTokens                      | number/integer          | High | x                     | x                         | Not Implemented     |
| Asset            | getId                              | string                  | High | x                     | x                         | Not Implemented     |
| Asset            | purchase                           | Order                   | High | Not Implemented       | x                         | Not Implemented     |
| Asset            | getDID                             | string                  | High | Not Implemented       | Not Implemented           | Not Implemented     |
| Asset            | getDDO                             | ddo                     | High | Not Implemented       | Not Implemented           | Not Implemented     |
| Asset            | publishMetadata                    | string                  | High | x                     | Not Implemented           | Not Implemented     |
| Asset            | getMetadata                        | Metadata                | High | x                     | Not Implemented           | Not Implemented     |
| Asset            | updateMetadata                     | boolean                 | High | x                     | Not Implemented           | Not Implemented     |
| Asset            | retireMetadata                     | boolean                 | Low  | x                     | Not Implemented           | Not Implemented     |
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
| SecretStore | encryptDocument | string       | High  | Not Implemented       | Not Implemented           | Not Implemented     |
| SecretStore | decryptDocument | string       | Hight | Not Implemented       | Not Implemented           | Not Implemented     |
| DID-DDO-lib | TBD             |              |       |                       |                           |                     |
| DID-DDO-lib | TBD ...         |              |       |                       |                           |                     |

## Examples

For examples please see [Tuna](https://github.com/oceanprotocol/tuna)
