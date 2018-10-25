# Ocean Cluster deployed in Kubernetes

## Table of Contents

  - [Architecture](#architecture)
  - [Connection details](#connection-details)

---

## Architecture

The cluster is deployed using a Helm package. A general view about the architecture:
![Ocean kubernetes cluster architecture](../img/ocean-kubernetes.png)

## Connection details

| Serice           | URL                                                                                |
|------------------|------------------------------------------------------------------------------------|
| keeper-contracts | a0974921fd78611e89c320e965e714bc-1426721097.us-east-1.elb.amazonaws.com:8545       |
| pleuston         | http://a09772bc5d78611e89c320e965e714bc-920324680.us-east-1.elb.amazonaws.com:3000 |
| aquarius         | a0979d861d78611e89c320e965e714bc-1571806593.us-east-1.elb.amazonaws.com:5000       |