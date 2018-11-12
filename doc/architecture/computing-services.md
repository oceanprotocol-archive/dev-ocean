
Table of Contents
=================

  * [Table of Contents](#table-of-contents)
   * [Motivation](#motivation)
   * [Architecture](#architecture)
      * [Enabling Publisher Services (Brizo)](#enabling-publisher-services-brizo)
      * [Responsibilities](#responsibilities)
      * [Flow](#flow)
      * [Next steps](#next-steps)


---

This page describes the highlights of the integration of additional computing services.

# Motivation

The most basic scenario of a Publisher, is to provide access to the Assets this Publisher owns or manage. 
In addition to this, having this data ecosystem in place,
Publishers could offer other related services in top of this. Some possibles scenarios are:

* The Publishers offer as a service the possibility of execute some computation on top of their data. This has some benefits:
  - The data **never** leaves the Publisher enclave
  - It's not necessary to move the data, the algorithm moves to the data
  - Data Protection Regulation. Having only the data once and not moving it, 
  makes easier to be compliant to the Data Protection Regulations applying to your data

* Storage services for new derived assets. As a result of the computation of the existing datasets, a new derived dataset could be created. Publishers could offer an additional service to make use of their existing storage capabilities.
  This is always optional, being possible to the users using the compute services, to download the new derived datasets created as a result of this computation.

# Architecture

## Enabling Publisher Services (Brizo)

The direct interaction with the infrastructure where the data is, requires the execution of a component handled by Publishers.
This component will be in charge of interact with the users, and manage the basics of own Publisher infrastructure to provide the additional services.
This business logic supporting the some additional Publisher capabilities, is responsibility of a new technical component.

The main & new key component introduced to support new Publisher services is named **Brizo**.

_"Brizo is an ancient Greek goddess who was known as the protector of mariners, sailors, and fishermen. She was worshipped primarily by the women of Delos, who set out food offerings in small boats. Brizo was also known as a prophet specializing in the interpretation of dreams."_

In the "Ocean ecosystem", Brizo is the technical component executed by the **Publishers** allowing to them to provide extended data services.
Brizo, as part of the Publisher ecosystem, includes the credentials to interact with the infrastructure (initially cloud, but could be on-premise).
Because of those credentials, the execution of Brizo **SHOULD NOT** be delegated to a third-party.

![Brizo High-Level Architecture](img/brizo-hl-arch.png)


## Responsibilities

The main responsibilities of the software are:

* Expose a HTTP API allowing to execute some extended data related services (compute and/or storage)
* Authorize the user on-chain using the proper Service Agreement. It allows to validate that user requesting the service is allowed to use that service.
* Interact with the infrastructure (cloud/on-premise) using the Publisher credentials.
* Start/stop/execute computing instances with the algorithm's provided by the users
* Retrieve the logs generated during the executions
* Register new Assets derivated of the executions as new Ocean Assets
* Provide **Proofs of Computation** to the Ocean Network

## Flow

![Sequence Diagram for computing services](img/computing-service-flow.png)

In the above diagram you can see the initial integration supported. It involves the following components/actors:

* Data scientists - Final users requiring to use some computing/storage services where the data they are using is.
* Ocean Keeper - In charge of enforcing the Service Agreement by tracing conditions.
* Publisher Infrastrucure - Cloud or On-Premise. Is were the Publisher data is.
* Publisher Agent (Brizo) - Orchestrate/provides the additional publisher services
* Publisher - Is the user owning the data and running Brizo. He/she doesn't interact actively in this flow.

The pre-conditions of this flow are:

* The Algorithm is implemented in one of the languages/frameworks supported by the Publisher
* The Publisher, specified in the Asset DDO the endpoint exposed by the Publisher (Brizo) to expose the computing services.
* Service Agreement template is already predefined and whitelisted `on-chain`.


### Steps

##### 1. Setup Service Agreement:

- The game begins with a data scientist is looking for a data asset but this data is attached to itsown compute service. 
He/She picks the asset, signs the agreement, then sends `signature, templateId` to the asset publisher. 
- The publisher setup a new service agreement instance `on-chain` using the data scientist (consumer's) signature.

##### 2. Fulfill Lock Payment Condition
- As a data scientist, he/she listens to `ExecuteAgreement` on-chain event, in order to `lock the payment` 
in the `PaymentCondition.sol` contract and fulfill the condition.
##### 3. Fulfill Upload Algorithm Condition
- The Data scientist, parses the DDO (using Squid) in order to see how the Publisher exposes a computing service.
The computing service defines, `how to upload an algorithm to the publisher side`, and `how to consume an output`. 
- The data scientist, uploads the algorithm `off-chain` directly to the data set publisher `algorithm endpoint`.
- Once, the algorithm file/s is/are uploaded, the data scientist calculates file/s hash, signs this hash and then submits 
the signature **ONLY** on-chain. 
- The publisher receives the algorithm file/s, calculates the hash(algorithm file/s) and submits this hash in order to fulfill `uploadAlgorithm` condition.
The keeper contracts automatically, verifies that both parties see the same files using (the `hash which is submitted by the publisher`, `signature submitted by data scientist` and `data scientist address`).
- Please do note that the software component that is responsible for running the algorithm, attaching data assets and runs the 
container is the [Brizo](https://github.com/oceanprotocol/brizo). Moreover, it pulls the logs periodically and saves the outputs.

##### 4. Fulfill Grant Access Condition

- In the mean while, the publisher starts computation machine/s, trains the model, collects logs and generates outputs (the derived asset).
Finally, The publisher archives and uploads the outputs.
- The publisher registers the derived asset (outputs) as a DID/DDO and assigns the ownership to the data scientist.
- The publisher grants access to the data scientist using `secret-store` and fulfill the grant access condition.

##### 5. Fulfill Release Payment
- Release Payment as a part of the reward function verifies all the above conditions. and the publisher
can call it once the granted access is fulfilled within a `timeout`. 
- The data scientist can consume the derived asset (outputs) by calling the secret store which in turn check the permissions 
on-chain then downloads the outputs using the decryption keys.

##### 6. Cancel Payment

The payment can be cancelled only if:
- The access to the derived asset (outputs) never delivered within timeout.
- Payment never locked.

##### 7. Agreement Fulfillement

The service agreement will be accepted as fulfilled agreement only if the `payment is released` to the service provider.


## Next steps

Next steps are:

* Adding extra condition for `proof of computation` as verifiable proof
* Integrate different cloud providers/on-premise infrastructure
* Support different kind of container flavours allowing to execute different kind of algorithms
* Model extended service agreements supporting richer services

