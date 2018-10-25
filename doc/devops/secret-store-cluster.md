# Parity Secret Store Cluster

## Table of Contents

  - [Architecture](#architecture)
  - [Connection details](#connection-details)
  - [Client details](#client-details)
  - [HTTP Interface](#http-interface)

---

[Parity Secret Store](https://wiki.parity.io/Secret-Store-Configuration) is a (beta) feature provided by Parity ethereum client. To help with the development we have a public cluster that can be used by anyone.

## Architecture

The cluster is deployed using Packer (for generating the AMI base image) and Terraform for deploying. A general view about the architecture:
![Secret store cluster architecture](../img/secret-store-cluster.png)

## Connection details

This parity secret store cluster is deployed in Amazon Web Services, and they are using a private ethereum network. Each server has a public ip address and a configured account. The connection details you have to add to you parity configuration file to join the same ethereum private network are:
```
bootnodes = [
  "enode://d69e03f7bc683c2de5a5ffc885e2a80bb8032e3d7787ba6be62665b2222719b2305d2d3de3b3ad68484b36b427f64b2b27b6c67849b9e90221254ec8fc505366@18.210.120.241:30303",
  "enode://c243f825107c251eec860ac154bc34f3e044a5f91489396bbcb3e9077a56aa3127b6e54ccc25ebc6a2f30682052df2aa0c8691011459d03b930f5142f1d602fe@35.171.30.2:30303",
  "enode://c9d572c977a06063e88c951ae2776145a5ccd906770a43c5db765f7c3707ffbf1aeda78e96d5e3e4f50d5d92a1a5282b4050ec4924d897a3dfc468af19a17e38@54.163.9.188:30303"
]
```


## Client details

The details about the client are:

|          | Address                                    | Public IP      | Password     |
|----------|--------------------------------------------|----------------|--------------|
| Client 1 | 0xdd781df818094d1fc7b115e7c47b753c9656bbd1 | 52.1.94.55     | Fx3bS1ifHQAe |
| Client 2 | 0xdcca2fbefa401bb63d29d3520ef733dc04844966 | 54.156.6.164   | xJ235B11OrVs |
| Client 3 | 0x33b312915313db4449944e5b97f6101d0c3af331 | 100.24.158.252 | paJ38FiKamhU |


## HTTP Interface

The secret store service offers an HTTP interface to communicate with the service. The service http interface is publicly exposed with an SSL proxy, and can be accessed using *`https://secret-store.dev-ocean.com`*. The DNS query will balance between the three secret store nodes.
