# Service Agreements in Ocean Protocol

Ocean Protocol is a network enabling discovery and trust between service (data and related services) providers and consumers. To enable delivery and payment for these services, we need pre-signed agreements between the providers and consumers. These service agreements can greatly help with setting expectations and defining terms of delivery in a permission-less network.

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

All services MUST be registered/published at one of the tribes/marketplaces associated with Ocean Protocol. The result of the registration of service SHOULD be generation of a service identifier which can be used to identify a service in a service agreement.

As part of publishing a service, the conditions of availability should also be published with the service metadata. For example,

1. If a data set is being published as a service, the state of data should be clearly mentioned - raw, clean, transformed, etc.
1. If an algorithm is being published as a service, the language in which the algorithm is available should also be published.

### Providers (serving meta-data and/or data)

// TODO

### Bundling of services

// TODO

### Sensitive data or large data (data that cannot leave its premises)

// TODO

### Third-party verification of service delivery

// TODO

### On-chain dispute resolution

// TODO

### Off-chain dispute resolution

// TODO

### Payments and escrows

// TODO

### Service agreement negotiation

Before the service agreements are deployed or invoked as smart-contracts on the Ocean keeper network, they should be negotiated off-chain between all the involved parties. Once all parties agree on the parameters and values in an agreement, then it SHOULD be invoked/initialized using the smart contracts.

// TODO - add more details - json data with key value pairs - values go in as smart contract invocation parameters

## Example services and agreements (templates)

// TODO
