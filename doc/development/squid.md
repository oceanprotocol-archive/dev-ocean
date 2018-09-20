# Squid API

## Ocean
- `js` Ocean(config(web3Provider, uri, gas, network))
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
- `js` requestTokens(senderAddress, numTokens)
- `py` sendPayment(assetId, order, publisherAddress, senderAddress)
- `js` payAsset(assetId, order, publisherAddress, senderAddress)
- `py` checkAsset(assetId)
- `js` checkAsset(assetId)
- `py` getAssetPrice(assetId)
- `js` getAssetPrice(assetId)

### `py` Acl `js` Auth
- `py` getOrderStatus(orderId)
- `js` getOrderStatus(orderId) 
- initiateAccessRequest(resourceID, providerAddress, pubKey, timeout)
- commitAccessRequest(id, isAvailable,  expirationDate,  discovery,  permissions,  accessAgreementRef,   accessAgreementType)
- deliverAccess Token(id, encryptedAccessToken)
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

### `py` Metadata
- getAssetMetadata(assetId)
- getAssets()
- getAsstesMetadata()
- publishAsset(providerId, assetId, publisherId, name, size, author, license, contentType, contentUrls, price, *)
- updateAsset(providerId, assetId, publisherId, name, size, author, license, contentType, contentUrls, price, *)
- retireAsset(providerId, assetId)

### Providers
- `py` registerProvider(url, provider_address)
- `py` getProviders()
- `py` getAssetProvider(assetId)

# Examples

List orders in JavaScript

```javascript
import { Ocean } from '@oceanprotocol/squid'

const ocean = await new Ocean({network: 'kovan'}).getInstance();
const orders = await ocean.getOrdersByConsumer('0x970e8f18ebfEa0B08810f33a5A40438b9530FBCF')

console.log(orders)
```