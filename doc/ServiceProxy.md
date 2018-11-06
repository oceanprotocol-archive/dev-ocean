# Introduction

## What is a service?

In the scope of this document, a service is a web service which has the following characteristics.

It is

* delivered over a REST HTTP interface
* "discoverable" API via Swagger/OpenAPI

## Why do we need Ocean services?

The primary artifacts available on the Ocean network is data assets. These data assets can be processed through
data pipelines that could run tasks such as:

* Data cleaning and pre-processing
* Model training
* Prediction services

Data services could benefit Ocean users by:

* Supporting a common library of data operations
* Incentivizing developers to provide common services
* Provenance tracking of a data workflow.

## Why do we need a service proxy

The [Service Level Agreements](https://github.com/oceanprotocol/dev-ocean/blob/feature/SLA-specs/doc/sla.md) document details the process for proofed, on-chain service integration with Ocean. That approach is applicable for services that 

* integrate on-chain by listening to service events
* provide proof of service delivery (e.g. Fitchain's proof-of-train)

However, this would mean that all existing web services would need an integration effort to work with Ocean, because:

* they need to understand service agreements.
* they need to choose an appropriate service agreement template.
* They need to provide proof of service delivery.

These stringent integration requirements increases the cost of service integrations 

Secondly, existing (web) services with customers (who are consuming the documented, public REST API) will not change their API to integrate with Ocean. 
(Ocean) consumers should be able to call these APIs inside the Ocean context.

A natural question at this point is: Why would Ocean consumers want to invoke these non-proofed services? Lets say that an Ocean user has a dataset and wants to join this dataset with external data from a service, such as a prediction service (which adds a predicted label column), or an external data feed (such as weather data).

Invoking these services can add a provenance trail which informs that these services 

* were invoked on a particular date/time
* optionally, store snapshots of the request and response payloads.

## Categories of services 

* Type 0: A type 0 service is agnostic to Ocean. It does not produce or consume Ocean assets, nor accept payment in Ocean tokens. 

* Type 1: A service has an Ocean identity, and can produce or consume Ocean assets. It may accept Ocean tokens, but does not listen to web3 events. 

* Type 2: a service as defined by service agreements.

## What is an Ocean service proxy?

A service proxy offers the following functionality:

It enables Type 0 and Type 1 to integrate with Ocean by;

* enabling the service owner to register the service on the Ocean Service proxy web API
* choose an appropriate service level agreement on the page
* grant permissions to Ocean Service Proxy to listen to service conditions on its behalf.
* Allow OSP to invoke the service and return results to the service consumer.

## Requirements

For the purposes of the discussion, we use two entities (as an example):

* Weather service: this is a public web service available via REST

* Ocean Service Proxy (OSP): this is an Ocean REST API. 

* The Weather service API must be a self-documenting API using Swagger/OpenAPI spec. 
* OSP must allow the weather service to be registered
* OSP must allow a consumer to invoke the weather API, and provide the path, payload and any other parameters.

## API Design

The service proxy has apis that can be divided into the following groups:

### Registry 

APIs to

* Register a new service
* De-commission a registered service
* List services in the registry

### Invocation

There can be two types of invocable services. 
The first is synchronous (e.g. a request to get the current weather), and the second kind is a, for lack of a better word, a *subscribable* service. The call to invoke; kicks off the service (e.g. training a model, or running a notebook for a fixed amount of time). 

* Make a invocation to a registered service 


## Sequence Diagram

[Image of Sequence diagram](https://github.com/oceanprotocol/dev-ocean/blob/feature/service-proxy/doc/img/Service_proxy.png)




