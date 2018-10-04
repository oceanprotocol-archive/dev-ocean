# Smart Service Level Agreement

This document provides technical details and describes the design of service agreements 
in ocean protocol.


## Definition

### Service

Service agreement is a commitment between provider/s and consumer of a service. In this commitment, the provider and 
consumer agree on quality, availability, and responsibilities involved in the service.

![](img/SLA_ServiceDefinition.png)

### Condition

A service provider defines one or more SLAs that apply to their services. Any defined  service 
between parties may/not be associated with a set of conditions
 and `asset/s`. In Ocean Protocol, a SLA is expressed as a set of `conditions` with 
 dependencies where any condition is defined as follows:
 
![SLA Condition Definition](img/SLA_ConditionDefinition.png)


- Control Smart Contract Address: This defines the business logic concerning one or more conditions
in the service agreement. This applies [Storage-Control Pattern](#storage-control-pattern)
- Function Fingerprint: For each condition in the control smart contract, there is a function 
fulfills only one condition
- Dependency Conditions (*Optional*): enforces the execution of the dependency model for this condition 
in terms of list of condition/s that must be fulfilled. The dependency model is defined in terms of tree structure 
in order to avoid any circular dependency and satisfy the `termination` and `correctness` of the model as shown below:

![SLA Dependency Model](img/SLA_DependencyModelDefinition.png)

## Components

### Conditions

In Ocean Protocol, Condition has two representations. The first representation defines 
the event and the associated action where stakeholders (Consumers, tribes, marketplaces, data scientist) store it ( in terms of json object) `off-chain`. 
This will enable parties to track the progress of the service agreement. This model is defined as 
[event-action model](#event-action-representation). The second representation is a compressed version of 
the json object and stored `on-chain`. This compressed version holds only the minimal required information 
in order to fulfill the service agreement.

#### Event-Action Representation

The below figure shows how the service parties interact based on [Trigger-Event-Action]() pattern which 
represents the core orchestration pattern in Ocean Service Agreement. 

* Definition
* Examples

### Roles of Ocean Software components
* Squid-lib
  *
* Provider node
  *
* Keeper contracts
  *
* Relay/event handler
  *
* UI/frontend interface/CLI
  *

### Flow
What are the steps involved in setting up and executing a service agreement. 

### Storage
How is the SLA stored and where

### Implementation of each component's own role in supporting the SLA flow
* Squid-lib
  *
* Provider node
  *
* Keeper contracts
  *
* Relay/event handler
  *
* UI/frontend interface/CLI
  *

## Appendix-1

## Storage-Control Pattern

TBC