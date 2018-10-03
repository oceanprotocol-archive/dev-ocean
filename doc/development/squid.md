# Squid API
## This is the New API, the old ones are listed at the bottom

## Ocean
- getInstance(config(web3Provider, nodeURI, gas, network, providerURI))
- getMessageHash(message) => hash of the given message
- createDIDRecord(content) => ocean specific DID with an id based on hash of the given string message
- registerProvider(url, provider_address)
- getProviders()

## Account 
- getAccounts() => list of accounts along with token and eth balances
- getTokenBalance()
- getEthBalance()
- requestTokens(amount) => bool

## Order
- purchaseAsset(assetDID, publisherId, price, timeout, conditions)
- getOrderStatus(orderId) => integer representing the order status as defined in the keeper 
- getOrders() => list of orders
- verifyOrderPayment(orderId) => true / false

## Asset / Metadata
- publishAsset(assetDID, assetDDO, price)
- updateAsset(assetDDO)
- retireAsset(assetDID)
- getAssets() => asset ids from keeper
- isAssetActive(assetDID) => true / false
- getAssetPrice(assetDID)
- getAssetMetadata(assetDID) => asset DDO
- getAssetsMetadata(<search-params>) => list of assets DDOs
- resolveAssetDID(did) => DDO of the given DID
- getAssets() => asset ids from keeper
- checkAsset(assetDID) => true / false
- getAssetConditions



# Old squid API

## Ocean
- `js` getInstance(config(web3Provider, nodeUri, gas, network, providerUri))
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