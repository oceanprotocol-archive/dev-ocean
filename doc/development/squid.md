# Squid API

Table of Contents
=================

   * [Squid API](#squid-api)
   * [Table of Contents](#table-of-contents)
      * [Common](#common)
         * [Getting an instance](#getting-an-instance)
            * [Functions](#functions)
      * [Ocean](#ocean)
         * [Balance](#balance)
            * [Functions](#functions-1)
         * [Tokens](#tokens)
            * [Functions](#functions-2)
         * [Decentralized Identifiers (DID)](#decentralized-identifiers-did)
            * [Functions](#functions-3)
         * [Helpers](#helpers)
            * [Functions](#functions-4)
      * [Assets](#assets)
            * [Functions](#functions-5)
      * [Service Agreements and Orders](#service-agreements-and-orders)
            * [Functions](#functions-6)
      * [Provider](#provider)
            * [Functions](#functions-7)
      * [Squid API Implementation state](#squid-api-implementation-state)
         * [Deleted](#deleted)
   * [Old squid API](#old-squid-api)
      * [Ocean](#ocean-1)
         * [Token](#token)
         * [Market](#market)
         * [py Acl <code>js</code> Auth](#py-acl-js-auth)
         * [py Web3 <code>js</code> helper](#py-web3-js-helper)
         * [Metadata](#metadata)
         * [Providers](#providers)
   * [Examples](#examples)



**This is the New API, the old ones are listed at the bottom**

## Common

Common functions exposed by an abstract class extended by all the other core classes

```java

/**
 * Abstract class providing the common methods like initilizers and similar
*/
abstract class SquidCommons {}

/**
 * Extends SquidCommons providing access to Ocean Core functionalities
*/
class OceanCore extends SquidCommons {}

/**
 * Extends SquidCommons providing access to Ocean Core functionalities
*/
class OceanCore extends SquidCommons {}

/**
 * Extends SquidCommons providing Asset functionalities
*/
class Assets extends SquidCommons {}

/**
 * Extends SquidCommons providing ServiceAgreements functionalities
*/
class ServiceAgreements extends SquidCommons {}

/**
 * Extends SquidCommons enabling Provider functionalities
*/
class Provider extends SquidCommons {}

```

### Getting an instance

#### Functions

```
getInstance(web3Dto, providerDto, gasPrice, gasLimit, nodeURI?, network?)
```

Parameters:

* web3Dto - Web3 client wrapped in a DTO object to avoid coupling with specific web3 specific libraries
* providerDto - Ocean Provider client wrapped in a DTO object
* gasPrice - Gas price to pay per write transactions
* gasLimit - Gas limit to pay per transaction
* nodeURI - ??
* network - is it needed ?

This method returns an instance of the **OceanManager** class. It allows to get access to the other Ocean core methods

## Ocean

Interface with core Ocean functions

### Balance


#### Functions

* **getOceanBalance** - The only parameter required is an account address (ie. 0x6309b5dd9245278a7fdfb2186dfb80583caeadc7). Returns the Ocean Tokens balance for that account.
```
oceanBalance= getOceanBalance(address)
```

* **getEtherBalance** - The only parameter required is an account address. Returns the Ether balance for that account.
```
ethBalance= getEtherBalance(address)
```

* **getBalance** - The only parameter required is an account address. Returns the Ocean tokens and Ether balance for that account.
```
balance= getBalance(address)
```


### Tokens

#### Functions

* **requestTokens** - Request a number of Ocean Tokens. Returns a boolean to know if everything was right.

```
result= requestTokens(amountTokens)
```


### Decentralized Identifiers (DID)

#### Functions

* **generateDID** - Generates a specific DID with a random id based on the given.

```
did= generateDID(seed)
```

* **resolveDID** - Given a DID the method returns the DDO associated to it.
```
ddo= resolveDID(did)
```


### Helpers

#### Functions

* **getMessageHash** - Generates a Hash in (format ???) using as input a given message.

```
hash= getMessageHash(message)
```


## Assets

#### Functions

* **publishMetadata** - Given an asset DDO, register this DDO off-chain. It returns a boolean with the result of the operation.
```
status= publishMetadata(assetDDO)
```

* **updateMetadata** - Given an asset DDO, update the DDO off-chain. This method replace the complete existing DDO by the DDO provided. It returns a boolean with the result of the operation.
```
status= updateMetadata(assetDDO)
```

* **retireMetadata** - Given an Asset DID, this method delete logically the Asset Metadata. **Nice to Have ??**
```
retireMetadata(assetDID)
```

//
* **getAssetMetadata** - Given a DID, returns the associated DDO. Internally calls Ocean.resolveDID(did).
```
ddo= getAssetMetadata(assetDID)
```

* **getAssetsMetadata** - Given an array of DID's, returns an array of the associated DDO's. Internally calls Ocean.resolveDID(did).
```
array[ddo]= getAssetsMetadata(array[assetDID])
```

* **getAssetPrice** - Get the asset price information existing on-chain. Shouldn't we move this to Service Agreements? The parameter should be a serviceAgreementId.
```
getAssetPrice(assetDID)
```

* **getAssetServiceAgreement** - Get a list of the service agreements published for a specific asset. providerId could be optional. Shouldn't we move this to Service Agreements?
```
getAssetServiceAgreement(assetDID, providerId)
```

* **checkAsset** - ?????
```
checkAsset(assetDID)
```

* **search** - Given a search query, returns a list of the DDO's matching with that query
```
array[ddo]= search(searchQuery)
```



## Service Agreements and Orders

#### Functions

* **publishServiceAgreement** -  The Publisher register a Service Agreement related with a specific Asset

```
serviceAgreementId= publishServiceAgreement(assetDID, providerId, price, ..)
```

* **retireServiceAgreement** - Given a Service Agreement id, the Publisher retire a Service Agreement
```
result= retireServiceAgreement(serviceAgreementId)
```

* **purchaseAsset** - Given a Service Agreement and Publisher Id, the Consumer purchase an asset
```
orderId= purchaseAsset(serviceAgreementId, publisherId , timeout)
```

* **getAssetAccess** -  Get all the information required to get access to an asset after the purchase process
```
asset= getAssetAccess(serviceAgreementId)
```

* **getOrderStatus** - Given an order id, returns an integer representing the order status as defined in the keeper
```
status= getOrderStatus(orderId)
```

* **getPurchasedOrders** - Return a list of orders purchased by the user (Consumer)
```
array[order]= getPurchasedOrders()
```

* **getServiceAgreements** - Return a list of the Servive agreements published by the user (Publisher)
```
array[serviceAgreement]= getServiceAgreements()
```

* **verifyOrderPayment** -  Given an order id, verifies if the order was paid
```
status= verifyOrderPayment(orderId)
```


## Provider

#### Functions

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


## Squid API Implementation state

Table not completed yet

| Category                  | Method                      | Python Implementation   | Javascript Implementation   | Java Implementation   |
|:--------------------------|:----------------------------|-------------------------|-----------------------------|-----------------------|
| Ocean                     | getInstance                 | Not Implemented         | Not Implemented             | Not Implemented       |
| Ocean                     | getOceanBalance             | Not Implemented         | Not Implemented             | Not Implemented       |
| Ocean                     | getEtherBalance             | Not Implemented         | Not Implemented             | Not Implemented       |
| Ocean                     | getBalance                  | Not Implemented         | Not Implemented             | Not Implemented       |
| Ocean                     | requestTokens               | Not Implemented         | Not Implemented             | Not Implemented       |
| Ocean                     | getMessageHash              | Not Implemented         | Not Implemented             | Not Implemented       |
| Ocean                     | generateDID                 | Not Implemented         | Not Implemented             | Not Implemented       |
| Ocean                     | resolveDID                  | Not Implemented         | Not Implemented             | Not Implemented       |




### Deleted

- Ocean(config(web3Provider, nodeURI, gas, network, providerURI))

Constructor is private so only way to get an instance is using the `getInstance()` method.

- getAccounts() => list of accounts along with token and eth balances

Not having an Actor registry make difficult to implement this. Maybe this business logic is not necessary.





# Old squid API

## Ocean
- `js` Ocean(config(web3Provider, nodeUri, gas, network, providerUri))
- `js` getInstance()
- `js` getAccounts()
- `js` getOrdersByConsumer(consumerAddress)
- `js` purchaseAsset(assetId, publisherId, price, privateKey, publicKey, timeout, senderAddress, initialRequestEventHandler, accessCommittedEventHandler, tokenPublishedEventHandler)

### Token
- `py` approve(address, resource_price)
- `py` getBalance(account)
- `js` getTokenBalance(accountAddress)
- `js` getEthBalance(account)

### Market
- `py` generateID(content)
- `py` verifyOrderPayment(orderId)
- `js` verifyOrderPayment(orderId) 
- `py` register(name, description, price, publisherAddress)
- `js` registerAsset(name, description, price, publisherAddress)
- `py` requestTokens(amount, address)
- `js` requestTokens(amount, address)
- `py` sendPayment(assetId, order, publisherAddress, senderAddress)
- `js` payAsset(assetId, order, publisherAddress, senderAddress)
- `py` checkAsset(assetId)
- `js` checkAsset(assetId)
- `py` getAssetPrice(assetId)
- `js` getAssetPrice(assetId)

### `py` Acl `js` Auth
- `py` getOrderStatus(orderId)
- `js` getOrderStatus(orderId) 
- `py` initiateAccessRequest(resourceID, providerAddress, pubKey, timeout)
- `py` commitAccessRequest(id, isAvailable,  expirationDate,  discovery,  permissions,  accessAgreementRef,   accessAgreementType)
- `py` deliverAccess Token(id, encryptedAccessToken)
- `py` getEncryptedAccessToken(orderId, senderAddress)
- `js` getEncryptedAccessToken(orderId, senderAddress)
- `py` cancelAccessRequest(orderId, senderAddress)
- `js` cancelAccessRequest(orderId, senderAddress)

### `py` Web3 `js` helper
- `py` sign(accountAddress, message)
- `js` sign(accountAddress, message)
- `py` getMessageHash(message)
- `py` toChecksumAddress(address)
- `py` toHex()
- `py` toBytes()
- `js` getAccounts()

### Metadata
- `py `getAssetMetadata(assetId)
- `py` getAssets()
- `py` getAsstesMetadata()
- `js` getAssetsMetadata()
- `py` publishAsset(providerId, assetId, publisherId, name, size, author, license, contentType, contentUrls, price, *)
- `js` publishDataAsset(asset)
- `py` updateAsset(providerId, assetId, publisherId, name, size, author, license, contentType, contentUrls, price, *)
- `py `retireAsset(providerId, assetId)

### Providers
- `py` registerProvider(url, provider_address)
- `py` getProviders()
- `py` getAssetProvider(assetId)

# Examples

List orders in JavaScript

```javascript
import { Ocean } from '@oceanprotocol/squid'

const ocean = await new Ocean({network: 'kovan'})
const orders = ocean.getOrdersByConsumer('0x970e8f18ebfEa0B08810f33a5A40438b9530FBCF')

console.log(orders)
```

Access market functionality in JavaScript

```javascript
import { Ocean } from '@oceanprotocol/squid'

const ocean = await new Ocean({network: 'kovan'})
const { market } = ocean

await market.verifyOrderPayment('0x970e8f18ebfEa0B08810f33a5A40438b9530FBCF')
```