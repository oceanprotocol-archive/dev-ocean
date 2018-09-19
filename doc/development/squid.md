# Squid API
## Token
- approve(address, resource_price)
- getBalance(account)

## Market
- generateID(content)
- verifyOrderPayment(orderId)
- register(name, description, price, publisherAddress)
- requestTokens(amount, address)
- sendPayment(assetId, order, publisherAddress, senderAddress)
- checkAsset(assetId)
- getAssetPrice(assetId)

## Acl
- getOrderStatus(orderId)
- initiateAccessRequest(resourceID, providerAddress, pubKey, timeout)
- commitAccessRequest(id,  isAvailable,  expirationDate,  discovery,  permissions,  accessAgreementRef,   accessAgreementType)
- deliverAccessToken(id, encryptedAccessToken)
- getEncryptedAccessToken(orderId, senderAddress)
- cancelAccessRequest(orderId, senderAddress)

## Web3
- sign(accountAddress, message)
- getMessageHash(message)
- toChecksumAddress(address)
- toHex()
- toBytes()

## Metadata

- getAssetMetadata(assetId)
- getAssets()
- getAsstesMetadata()
- publishAsset(providerId, assetId, publisherId, name, size, author, license, contentType, contentUrls, price, *)
- updateAsset(providerId, assetId, publisherId, name, size, author, license, contentType, contentUrls, price, *)
- retireAsset(providerId, assetId)

## Providers

- registerProvider(url, provider_address)
- getProviders()
- getAssetProvider(assetId)