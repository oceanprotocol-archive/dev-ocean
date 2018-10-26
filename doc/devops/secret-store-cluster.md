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
  "enode://16bddd0215f9bb8a9328671b7b1554a7811cf310065fde04248ce4cea92fe2831412894c912e6b9e45019d5011631452055a5c1873f2d6760baa95f51c449787@18.210.120.241:30303",
  "enode://ede2b4f857b19dfd2e93fa868b58a2cce83e9d813f49ca6d51315dd5e6043d35085f56ed65a53db357761e0ef23d91c96f627fdcc784cc5b14d2cb5cdab02b90@35.171.30.2:30303",
  "enode://35294b950d2effa982642a04d7265ba20b82c0f3622da14ce72791484eb0252ea273fd2acd342c241df1a9a91d5e9c29fc932600670e674e1a5132511decbbd7@54.163.9.188:30303"
]
```

## Client details

The details about the client are:

|          | Address                                    | Public IP      |
|----------|--------------------------------------------|----------------|
| Client 1 | 0x64137af0104d2c96c44bb04ac06f09ec84cc5ae4 | 52.1.94.55     |
| Client 2 | 0xd81934365c02da201dc2b85e401ca75b48f7ccbd | 54.156.6.164   |
| Client 3 | 0x852eab9a4529cce71aa3f191da6900d57c802881 | 100.24.158.252 |


## HTTP Interface

The secret store service offers an HTTP interface to communicate with the service. The service http interface is publicly exposed with an SSL proxy, and can be accessed using *`https://secret-store.dev-ocean.com`*. The DNS query will balance between the three secret store nodes.
