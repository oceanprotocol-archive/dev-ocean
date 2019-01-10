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
ocean = Ocean(Config('config.ini'))
```

In JavaScript:

```js
import { Ocean } from '@oceanprotocol/squid'

const ocean = await Ocean.getInstance({...})
```

## Ocean

This class serves as the interface with Ocean Protocol. The Ocean class aggregates the list of **Account**s (ethereum accounts), list of **Asset**s, and a list of **Order** objects. The Ocean class aggregates the Keeper class, which in turn interfaces with the running Smart Contracts: Market, Token, and Auth.

### getAccounts

ASYNC. Returns all available accounts loaded via a wallet, or by Web3.

```js
const accounts = ocean.getAccounts()
```

### searchAssets

ASYNC. Given a search query, returns a list of the Asset objects matching with that query.

```js
const ddoList = ocean.searchAssets(searchQuery)
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

ASYNC. Given a search text query, returns a list of the DDO objects matching with that text query.

```
const ddoList = ocean.searchAssetsByText('office')
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
 
### registerAsset

ASYNC. High-level method publishing the metadata off-chain and registering the Service Agreement on-chain. It orchestrate the publishAssetMetadata and publishServiceAgreement, and creating a DDO methods.

```js
const metadata = {...}
const assetDDO = ocean.registerAsset(metadata, publisherAccount)
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


### purchaseAssetService

ASYNC. Start the purchase of an asset's service. It first signs the service agreement then sends the request 
to the publisher (Brizo http service)
```js
const serviceAgreementId = ocean.purchaseAssetService(did, serviceDefinitionId, consumerAccount)
```

### executeServiceAgreement

ASYNC. Execute the ServiceAgreement on-chain which starts the processing of the service agreement 
events. This occurs after starting a purchase via the `purchaseAssetService` 
```js
const transactionReceipt = ocean.executeServiceAgreement(
    did, 
    serviceDefinitionId, 
    serviceAgreementId, 
    serviceAgreementSignature, 
    consumerAddress, 
    publisherAccount)
```

### resolveAssetDID

ASYNC. Given a DID, return the associated DID Document (DDO). The DDO is resolved by directly 
interacting with the keeper node to retrieve the metadata store's (Aquarius) URL. This URL is 
used to fetch the DDO from the off-chain ocean-db storage (mongodb, bigchain-db, or whatever)

```js
const assetDDO = ocean.resolveAssetDID(did)
```

### isAccessGranted
Checks whether the given account was granted access to the specified asset did. Returns boolean
```js
const grantedAccess = ocean.isAccessGranted(serviceAgreementId, did, consumerAccount)
```

## Account

Ocean Account object.

### getId

ASYNC. Return the Id used by this account.

```js
const id = account.getId()
```

### getOceanBalance

ASYNC. Returns the Ocean Tokens balance for that account.

```js
const oceanBalance = account.getOceanBalance()
```

### getEtherBalance

ASYNC. Returns the Ether balance for that account.

```js
const ethBalance = account.getEtherBalance()
```

### getBalance

ASYNC. Returns an instance of the Balance object for that account.

```js
const balance = account.getBalance()
```

### requestTokens

ASYNC. Request a number of Ocean Tokens. Returns the number of tokens accounted.

```js
const amount = account.requestTokens(amountTokens)
```

### unlock
ASYNC. Unlocks the eth account if password is provided (Python only). The unlock 
applies only to the next transaction, the account locks automatically after the 
transaction.

```python
account.unlock()
``` 

## DDO

Interface provides access to assets DID Document.

### getId

SYNC. Return the ID used by this asset.

```js
id= ddo.getId()
```

### getDID

SYNC. Return the DID used by this asset.

```js
did = ddo.getDID()
```

### getMetadata

SYNC. returns the metadata stored off chanin by using the asset DDO. The metadata is stored in the ddo as a `Metadata` service.

```js
const metadata = asset.getMetadata()
```

## DID-DDO-Library (Private)

TBD

## Squid API Implementation state

Public API

| Class            | Method                             | Return Value            | Prio | Python Implementation | Javascript Implementation | Java Implementation |
| :--------------- | :--------------------------------- | :---------------------- | :--- | :-------------------- | :------------------------ | :------------------ |
| Ocean            | getInstance (js, java)/ Ocean (py) | Ocean                   | High | Not Implemented       | x                         | Not Implemented     |
| Ocean            | getAccounts                        | array[Account]          | High | x                     | x                         | Not Implemented     |
| Ocean            | registerAsset                      | DDO                     | High | x                     | x                         | Not Implemented     |
| Ocean            | searchAssets                       | array[DDO]              | High | x                     | x                         | Not Implemented     |
| Ocean            | searchAssetsByText                 | array[DDO]              | High | x                     | x                         | Not Implemented     |
| Ocean            | resolveAssetDID                    | DDO                     | High | x                     | x                         | Not Implemented     |
| Ocean            | isAccessGranted                    | boolean                 | High | x                     | Not Implemented           | Not Implemented     |
| Ocean            | purchaseAssetService               | hex str (agreement id)  | High | x                     | x                         | Not Implemented     |
| Ocean            | executeServiceAgreement            | hex str (tx. hash)      | High | x                     | x                         | Not Implemented     |
| Ocean            | consumeService                     | None                    | High | x                     | Not Implemented           | Not Implemented     |
|                  |                                    |                         |      |                       |                           |                     |
| Account          | getId                              | string                  | High | x                     | x                         | Not Implemented     |
| Account          | getOceanBalance                    | number/integer          | High | x                     | x                         | Not Implemented     |
| Account          | getEtherBalance                    | number/integer          | High | x                     | x                         | Not Implemented     |
| Account          | getBalance                         | Balance                 | High | x                     | x                         | Not Implemented     |
| Account          | requestTokens                      | number/integer          | High | x                     | x                         | Not Implemented     |
| Account          | unlock                             | None                    | High | x                     | Not Implemented           | Not Implemented     |
|                  |                                    |                         |      |                       |                           |                     |
| DDO              | findServiceByType                  | Service                 | High | x                     | x                         | Not Implemented     |
| DDO              | findServiceById                    | Service                 | High | x                     | x                         | Not Implemented     |
| DDO              | getMetadata                        | json                    | High | x                     |                           |                     |
|                  |                                    |                         |      |                       |                           |                     |

## Common error messages and error codes
TODO


## Examples

For examples, see the page listing [Tools & Examples on the Ocean Protocol docs site](https://docs.oceanprotocol.com/concepts/tools/).
