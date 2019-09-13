# Compute to the Data Tests Plan


## Objective

The intention of this document is to identify the different test scenarios to be executed to validate the correct behavior of the end to end solution.

## Test Cases

### End user tests

#### Publishing an Algorithm

As a Data Scientist I would like to publish an algorithm in Ocean Protocol. 
The description of the algorithm to publish can be found here: 
https://github.com/oceanprotocol/OEPs/blob/master/8/v0.4/ddo-example-algorithm.json


#### Publishing a Workflow

As a Data Scientist I would like to publish an algorithm in Ocean Protocol. 
The Workflow to publish can be found here: 
https://github.com/oceanprotocol/OEPs/blob/master/8/v0.4/ddo-example-workflow.json

#### Delegation of Asset permissions

Using a Squid implementation a Data Owner must be able to delegate the permissions to a different user.

This should be using under the hood the following Squid methods:

* `ocean.assets.delegatePermissions`
* `ocean.assets.revokePermissions`
* `ocean.assets.getPermissions`

Tests:

- [ ] A data owner delegate the permissions of an asset to a different address (Squid-Java)
- [ ] A data owner get the permissions of a specific asset (Squid-Java)
- [ ] A data owner revoke the permissions of an asset to a subject (Squid-Java)

- [ ] A data owner delegate the permissions of an asset to a different address (Squid-Python)
- [ ] A data owner get the permissions of a specific asset (Squid-Python)
- [ ] A data owner revoke the permissions of an asset to a subject (Squid-Python)

- [ ] A data owner delegate the permissions of an asset to a different address (Squid-JS)
- [ ] A data owner get the permissions of a specific asset (Squid-JS)
- [ ] A data owner revoke the permissions of an asset to a subject (Squid-JS)

- [ ] A data owner delegate the permissions of an asset to a different address (CLI)
- [ ] A data owner get the permissions of a specific asset (CLI)
- [ ] A data owner revoke the permissions of an asset to a subject (CLI)


#### Transfer the Ownership of an Asset

Using a Squid implementation (directly or CLI) a Data Owner must be able to transfer the ownership of an asset already published.

This should be using under the hood the Squid `ocean.assets.transferOwnership` method.

Tests: 

- [ ] Transfer the ownership of an Asset using Squid-Java
- [ ] Transfer the ownership of an Asset Squid-Py
- [ ] Transfer the ownership of an Asset Squid-JS
- [ ] Transfer the ownership of an Asset Ocean CLI


#### Publishing a Dataset with a Compute Service

As a Data and Compute Provider, a user must be able to publish a new Compute service associated to an asset. 

An example of a compute service can be found here:
https://github.com/oceanprotocol/OEPs/blob/master/12/ddo.computing.json

- [ ] Publishing a Compute Service using Squid-Java
- [ ] Publishing a Compute Service Squid-Py
- [ ] Publishing a Compute Service Squid-JS
- [ ] Publishing a Compute Service Ocean CLI

This should be using under the hood the Squid `ocean.services.createComputeServiceExecution` method.


#### Order a Compute Service

Using a Squid implementation (directly or CLI) a Data Scientist must be able to order a compute service already published.

This should be using under the hood the Squid `ocean.agreements.conditions.grantServiceExecution` method.

Tests: 

- [ ] Order a compute service using Squid-Java
- [ ] Order a compute service using Squid-Py
- [ ] Order a compute service using Squid-JS
- [ ] Order a compute service using Ocean CLI

#### Execute a Compute Service

Using a Squid implementation (directly or CLI) a Data Scientist must be able execute a previously purchased compute service.

This should be using under the hood the Squid `ocean.assets.execute` method.

Tests: 

- [ ] Execute a compute service using Squid-Java
- [ ] Execute a compute service using Squid-Py
- [ ] Execute a compute service using Squid-JS
- [ ] Execute a compute service using Ocean CLI


### Operator Tests

#### Register a new Workflow in the Operator Service



#### Register multiple Workflows in the Operator Service


## Configuration

### Spree

#### Accounts

* Data Owner: `0x`
* Data Scientist: `0x`
* Provider (Brizo, Pods, etc): `0x`
* Aquarius Url: `https://`
* Brizo Url: `https://`
* K8s namespace: ``

#### Assets

* Dataset 1 with Compute Service: `did:op:`
* Dataset 2 with Compute Service: `did:op:`
* Dataset 3 with Compute Service: `did:op:`
* Algorithm 1: `did:op:`
* Algorithm 2: `did:op:`
* Algorithm 3: `did:op:`
* Workflow 1: `did:op:`
* Workflow 2: `did:op:`
* Workflow 3: `did:op:`



### Duero