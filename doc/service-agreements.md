# Service Agreements in Ocean Protocol

Ocean Protocol is a network enabling discovery and trust between service (data and related services) providers and consumers. To enable delivery and payment for these services, we need pre-signed agreements between the providers and consumers. These service agreements can greatly help with setting expectations and defining terms of delivery in a permission-less network.

The service-agreement in Ocean Protocol will be deployed and executed on-chain, on the Ocean keeper network. For this to work seamlessly, its important that the primitives for service agreements should be identified.

This document provides an over-view or a blueprint for Service Agreements in Ocean Protocol and their primitives.

This is a work in progress.

## Services

On a high-level, the services published and provided as part of a marketplace based on Ocean Protocol are,

1. Data (data sets)
1. Algorithms
1. Compute
1. Storage
1. Verification of compute
1. Combination of some of the above

## Service Agreements

For providing and consuming the services defined above, a service agreement should define the following elements,

1. Service provider(s) and consumer - The agreement should identify the involved parties in the service delivery.
1. Scope of delivery - The service agreement should clearly define what services are to be provided.
1. Conditions of delivery - The service agreement should also define the conditions of delivery of services. These conditions should be proposed by the provider and should be agreed on by the consumer.
1. Verification (optional)- If needed, a service agreement should define the conditions and scenarios for verification of delivery of service.
1. Disputes and settlement - A service agreement should define a process for handling disputes between the service provider and consumer. The dispute resolution can be on-chain (verification and/or voting) or off-chain (third party arbitration).
1. Payment - The service agreement should also provide the terms and conditions of payment from the consumer to the provider.

## Service Agreements in context of Ocean Components

In the context of Ocean Protocol components, following scenarios are important for setting up service agreements.

### Tribes and/or marketplaces (publishing data sets)

All services must be registered/published at one of the tribes/marketplaces associated with Ocean Protocol. The result of the registration of service should be generation of a service identifier which can be used to identify a service in a service agreement.

As part of publishing a service, the conditions of availability should also be published with the service metadata. For example,

1. If a data set is being published as a service, the state of data should be clearly mentioned - raw, clean, transformed, etc.
1. If an algorithm is being published as a service, the language in which the algorithm is available should also be published.

### Providers (serving meta-data and/or data)

In some cases, the data will be served by the provider *on behalf of* the data-set owner. In these cases, the service agreement should clearly mention the data-set owner and the provider. The data-set owner is providing data as service and the provider is providing hosting infrastructure as a service.

If a provider is proving additional services on top of data - compute, etc. then they should also be published on the market-place.

### Bundling of services

Ocean being a multi-party marketplace for data and associated services, there will be scenarios when a sub-set of services from different providers can be bundled together to have more value for a data set. For example,

1. Data + compute
1. Data + algorithm
1. Data + algorithm + compute
1. and so on...

In scenarios such as these, it's important to have lower-level service agreements which can combine to form a high-level service agreement. If a data set owner and a compute provider would like to create a combined offering by associating with each other, they should first have a service agreement between each other. For example, in this lower-level service agreement, the data-set owner can be the consumer and the compute provider can be the service-provider.

![multi-level service agreements](./img/service-agreements.png)

The overall idea is to support hierarchical service-agreements so that services can be bundled together for a better offering to the end-consumer. This would also reduce the responsibility on the consumer to find all the relevant providers for an end-to-end service.

### Sensitive data or large data (data that cannot leave its premises)

If a data owner is publishing a data set which is too large or too sensitive to leave its premises, it's important for better consumption, that they must provide and bundle the compute service with the data. The service agreement between the data owner and the consumer will then be prepared accordingly.

### Third-party verification of service delivery

Third party verifiers are also considered service providers in the context of Ocean Protocol. If a service provided needs third-party verification then the verification service should also be part of the same service agreement. This would then be a service agreement between at least three parties - consumer, service provider(s) and verifiers (also service providers). The verification service should also be registered/published via an Ocean marketplace.

### On-chain dispute resolution

The service agreements should provide a mechanism to support on-chain dispute resolution.

// TODO

### Off-chain dispute resolution

// TODO

### Payments and escrows

// TODO

### Service agreement negotiation

Before the service agreements are deployed or invoked as smart-contracts on the Ocean keeper network, they should be negotiated off-chain between all the involved parties. Once all parties agree on the parameters and values in an agreement, then it should be invoked/initialized using the smart contracts.

// TODO - add more details - json data with key value pairs - values go in as smart contract invocation parameters

## Example services and agreements (templates)

// TODO
