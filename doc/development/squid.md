# Squid API

Table of Contents
=================

   * [Squid API](#squid-api)
      * [This is the New API, the old ones are listed at the bottom](#this-is-the-new-api-the-old-ones-are-listed-at-the-bottom)
      * [Ocean](#ocean)
         * [Getting an instance](#getting-an-instance)
         * [Tokens](#tokens)
         * [Balance](#balance)
            * [Request Tokens](#request-tokens)
         * [Decentralized Identifiers (DID)](#decentralized-identifiers-did)
         * [Helpers](#helpers)
      * [Assets](#assets)
      * [Service Agreements // Orders](#service-agreements--orders)
      * [Provider](#provider)
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

## Ocean

Interface with core Ocean functions

### Getting an instance

```
OceanManager.getInstance(web3Dto, providerDto, gasPrice, gasLimit, nodeURI?, network?)
```

Parameters:

* web3Dto - Web3 client wrapped in a DTO object to avoid coupling with specific web3 specific libraries
* providerDto - Ocean Provider client wrapped in a DTO object
* gasPrice - Gas price to pay per write transactions
* gasLimit - Gas limit to pay per transaction
* nodeURI - ??
* network - is it needed ?

This method returns an instance of the **OceanManager** class. It allows to get access to the other Ocean core methods

### Tokens

### Balance

```
// Returns the Ocean Tokens balance for a specific account
oceanBalance= getOceanBalance(address)

// Returns the Ether balance for a specific account
ethBalance= getEtherBalance(address)

// Returns the Ocean Tokens & Ether balance for a specific account
balance= getBalance(address)
```

The only parameter required is the account address (i.e. 0x6309b5dd9245278a7fdfb2186dfb80583caeadc7)
It returns the balance of Ocean tokens and/or Ether

#### Request Tokens

```
result= requestTokens(amountTokens)
```

Request a number of Ocean Tokens. Returns a boolean to know if everything was right.


### Decentralized Identifiers (DID)

```
did= generateDID(seed)
```

This method generates a specific DID with a random id based on the given

```
ddo= resolveDID(did)
```

Given a DID the method returns the DDO associated to it

### Helpers

```
hash= getMessageHash(message)
```

Generates a Hash in (format ???) using as input a given message


## Assets

```
//
publishMetadata(assetDDO)

//
updateMetadata(assetDDO)

// Nice to Have?
retireMetadata(assetDID)

// internally calls Ocean.resolveDID(did)
getAssetMetadata(assetDID)

//
getAssetsMetadata(array[assetDID])

//
getAssetPrice(assetDID)

// Get a list of the service agreements published for a specific asset. providerId could be optional
getAssetServiceAgreement(assetDID, providerId)

// ?????
checkAsset(assetDID)

//
search(searchQuery)
```



## Service Agreements // Orders

```
// The Publisher register a Service Agreement related with a specific Asset
serviceAgreementId= publishServiceAgreement(assetDID, providerId, price, ..)

// The Publisher retire a Service Agreement
result= retireServiceAgreement(serviceAgreementId)

// The Consumer purchase an Asset
orderId= purchaseAsset(serviceAgreementId, publisherId , timeout)

// Get all the information required to get access to an asset after the purchase process
asset= getAssetAccess(serviceAgreementId)

// Integer representing the order status as defined in the keeper
status= getOrderStatus(orderId)

// Return a list of orders purchased by the user (Consumer)
array[order]= getPurchasedOrders()

// Return a list of servive agreements published by the user (Publisher)
array[serviceAgreement]= getServiceAgreements()

// Verify if the order was paid
status= verifyOrderPayment(orderId)
```


## Provider

```
//
result= registerProvider(providerAddress, url)

//
getProviders()

// Get the Providers giving access to an Asset
getAssetProviders(assetDID)
```




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