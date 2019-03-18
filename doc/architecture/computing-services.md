---
title: Computing Services
description: How Ocean Protocol enables Publishers to provide computing services and related services.
slug: /concepts/computing-services/
section: concepts
---

**NOTICE: Because the Keeper Contracts have been refactored, this spec is now out-of-date. It should be updated or replaced.**

## Motivation

The most basic scenario for a Publisher is to provide access to the datasets they own or manage.
In addition to that, a Publisher could offer other data-related services.
Some possibilities are:

1. A service to execute some computation on top of their data. This has some benefits:

   - The data **never** leaves the Publisher enclave.
   - It's not necessary to move the data; the algorithm is sent to the data.
   - Having only one copy of the data and not moving it makes it easier to be compliant with data protection regulations.

2. A service to store newly-derived datasets. As a result of the computation on existing datasets, a new dataset could be created. Publishers could offer a storage service to make use of their existing storage capabilities. This is optional; users could also download the newly-derived datasets.

## Architecture

### Enabling Publisher Services (Brizo)

The direct interaction with the infrastructure where the data resides requires the execution of a component handled by Publishers.

This component will be in charge of interacting with users and managing the basics of a Publisher's infrastructure to provide these additional services.

The business logic supporting these additional Publisher capabilities is the responsibility of this new technical component.

The main and new key component introduced to support these additional Publisher services is named **Brizo**.

> Brizo is an ancient Greek goddess who was known as the protector of mariners, sailors, and fishermen. She was worshipped primarily by the women of Delos, who set out food offerings in small boats. Brizo was also known as a prophet specializing in the interpretation of dreams.

In the Ocean ecosystem, Brizo is the technical component executed by the **Publishers**, which provides extended data services. Brizo, as part of the Publisher ecosystem, includes the credentials to interact with the infrastructure (initially in cloud providers, but it could be on-premise).

Because of these credentials, the execution of Brizo **SHOULD NOT** be delegated to a third-party.

<repo name="brizo"></repo>

![Brizo High-Level Architecture](img/brizo-hl-arch.png)

### Responsibilities

The main responsibilities of Brizo are:

* Expose an HTTP API allowing for the execution of some extended, data-related services (compute and/or storage).
* Authorize the user on-chain using the proper Service Agreement. That is, validate that the user requesting the service is allowed to use that service.
* Interact with the infrastructure (cloud/on-premise) using the Publisher's credentials.
* Start/stop/execute computing instances with the algorithms provided by users.
* Retrieve the logs generated during executions.
* Register newly-derived assets arising from the executions (i.e. as new Ocean assets).
* Provide **Proofs of Computation** to the Ocean Network.

### Flow

![Sequence Diagram for computing services](img/computing-service-flow.png)

In the above diagram you can see the initial integration supported. It involves the following components/actors:

* Data Scientists - The end users who need to use some computing/storage services offered by the same Publisher as the data Publisher.
* Ocean Keeper - In charge of enforcing the Service Agreement by tracing conditions.
* Publisher Infrastructure - The storage and computing systems where the Publisher's data resides (in a cloud provider or on-premise).
* Publisher Agent (Brizo) - Orchestrates/provides additional Publisher services.
* Publisher - The actor running Brizo and other software. The Publisher is often the same as the data owner.

Before the flow can begin, the following pre-conditions must be met:

* The Algorithm must be implemented in one of the languages/frameworks supported by the Publisher.
* The Asset DDO must specify the Brizo endpoint exposed by the Publisher.
* The Service Agreement template must already be predefined and whitelisted `on-chain`.

The following describes the steps in the flow.

#### 1. Set Up the Service Agreement

- The game begins with a data scientist looking for a data asset and finding one where the data is attached to its own compute service. They pick the asset, sign the agreement, then send `signature, templateId` to the asset Publisher.
- The Publisher sets up a new Service Agreement instance on-chain, using the data scientist's signature.

#### 2. Fulfill the Lock Payment Condition

- The data scientist listens for the `ExecuteAgreement` on-chain event, in order to `lock the payment` in the `PaymentCondition.sol` smart contract and fulfills the condition.

#### 3. Fulfill the Upload Algorithm Condition

- The data scientist parses the DDO (using Squid) to see how the Publisher exposes a computing service. The computing service defines how to upload an algorithm to the Publisher side and how to consume an output.
- The data scientist uploads the algorithm _off-chain_ directly to the data set Publisher's algorithm endpoint.
- Once the algorithm files are uploaded, the data scientist calculates the files' hash, signs this hash and then submits **ONLY** the signature on-chain.
- The Publisher receives the algorithm files, calculates the hash (algorithm files) and submits this hash to fulfill the `uploadAlgorithm` condition. The Keeper contracts automatically verify that both parties see the same files using 1) the hash which is submitted by the Publisher; 2) the signature submitted by the data scientist; and, 3) the data scientist's address.
- Please note that the software component that is responsible for running the algorithm, attaching data assets, and running the container is Brizo. Moreover, it pulls the logs periodically and saves the outputs.

#### 4. Fulfill the Grant Access Condition

- Meanwhile, the Publisher starts computation, trains the model, collects logs and generates outputs (the derived assets). Finally, the Publisher archives and uploads the outputs.
- The Publisher registers the derived assets (outputs) as DIDs/DDOs and assigns ownership to the data scientist.
- The Publisher grants access to the data scientist using the Secret Store and fulfills the grant access condition.

#### 5. Release Payment

- Once the grant access condition is fulfilled (within a `timeout`), the Publisher can release payment.
- The data scientist can consume the derived assets (outputs) by calling the Secret Store which in turn checks the permissions on-chain, and then downloads the outputs using the decryption keys.

#### 6. Cancel Payment

The payment can be cancelled only if:

- The access to the derived assets (outputs) was not delivered within timeout period, or
- Payment was never locked.

#### 7. Agreement Fulfillment

The Service Agreement will be accepted as fulfilled only if the payment is released to the service provider.

## Next Steps

Some next steps may include:

* Adding an extra condition for a _proof of computation_ as verifiable proof.
* Integrating different cloud providers/on-premise infrastructure.
* Supporting different kinds of containers, allowing for the execution of different kinds of algorithms.
* Modelling extended Service Agreements that support richer services.
