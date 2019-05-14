# Ocean Parity Installation Guide

## Introduction

This guide started out as a guide to setting up and running a keeper node in the Nile Testnet. Its scope expanded to include other networks, including the Duero Testnet and production networks. Production networks have higher security requirements.

A keeper node is basically a computer running [Parity Ethereum](https://www.parity.io/ethereum/), configured to be part of a particular Ethereum network: one where the Ocean Protocol keeper contracts have been (or will be) deployed. In particular, this guide is about how to set up an _authority node_ (not a user node, not a secret store node) in a ]Parity Proof of Authority (PoA) network](https://wiki.parity.io/Proof-of-Authority-Chains).

Note: Parity Ethereum is sometimes called just "Parity." Parity is also the name of the company behind Parity Ethereum, but the context should make it clear what is meant.

## Requirements

As stated in Parity documentation (https://wiki.parity.io/FAQ#what-are-the-parity-ethereum-disk-space-needs-and-overall-hardware-requirements), the requirements would be:

> Running a full node with the standard configuration for the Ethereum Mainnet requires a lot of computer resources. The blockchain download and validation process are particularly heavy on CPU and disk IO. It is therefore recommended to run a full node on a computer with multi-core CPU, 4GB RAM and an SSD drive and at least 200GB free space. Internet connection can also be a limiting factor. A decent DSL connection is required.

### Cloud requirements

 - You can choose the cloud provider and service that fits better to your requirement. For simplicity and general support, we have used the computing services (Aws EC2 and Azure VMs) to set up our nodes, but other services would be possible to be used also.

 - A medium size running instance (8vCPUs & 16GB RAM) must be enough for executing an authority of the network

 - As stated in Ethereum specification, the Enode URL format (https://github.com/ethereum/wiki/wiki/enode-url-format) does not allow DNS names. Due to this restriction this addresses must be specified using IP Addresses. Depending on your cloud provider the possibilies to use a public ip address may vary, so please consider it when you think about which service may fit better to the case.
 
 - Expose the Ethereum listener (30303/tcp) and discovery (30303/udp) publicly. Additionaly you can expose the port 8545 (rpc interface) locally or publicly, but taking care of the implications of exposing this publicly.


## Installation guide

The following instructions has been used to install a node in Centos 7 and Ubuntu 18.04. The external technologies used are systemd and docker, both widely used today. In any case, if there are any inconvenience in using this two technologies there is not any technical limitation to run Parity without using any of them, so please feel free to deploy/configure it in the way you feel more confortable.

The installation consist in a docker container that runs the Parity Client. It's highly recommended to configure a SSL Proxy on top of the Keeper public interface (port 8545). The HTTP plain trafic with the Keeper must be restricted, being all the incoming trafic going through the SSL proxy (Nignx or similar). 

The Ocean Protocol network makes use of the [Parity Secret Store](https://blog.oceanprotocol.com/secure-on-chain-access-control-for-ocean-protocol-38dca0af820c?source=collection_home---5------18---------------------). So if you want to run a Parity node as part of the network, it's necessary to run a client with the Secret Store extensions enabled.

We package the stable versions of [Parity Ethereum](https://github.com/oceanprotocol/parity-ethereum) including the Secret Store extensions. So we recommend to use our [Parity Docker image](https://hub.docker.com/r/oceanprotocol/parity-ethereum) to make easier the deployment of your node.


The steps to follow are:

1. Install Docker CE. Please check [the Docker official documentation](https://docs.docker.com/install/) for a detailed guide.

2. Configure the systemd unit:

```bash
cat <<EOF | sudo tee /etc/systemd/system/parity.service
[Unit]
Description=Docker Container %N
Requires=docker.service
After=docker.service

[Service]
Restart=always
ExecStart=/usr/bin/docker run\
 --name %N\
 --restart=always\
 -p 30303:30303/tcp\
 -p 30303:30303/udp\
 -p 8545:8545\
 -p 8546:8546/tcp\
 --volume=/parity_data/:/parity_data/\
 --volume=/etc/parity/:/etc/parity/\
 --entrypoint=/opt/parity/parity\
 parity/parity:v2.3.3\
 --config /etc/parity/config.toml
ExecStop=/usr/bin/docker rm -f %N

[Install]
WantedBy=default.target
EOF

sudo chmod 644 /etc/systemd/system/parity.service
```

Please note:

- The ports exposed from the container to the host are 30303/tcp (ethereum listener), 30303/udp (ethereum discovery) , 8545 (rpc interface) and 8546 (websocket interface). You can modify the host's ports (the first ports) if you want to modify the exposed ports in the host.
- The volume /parity_data is the `base_path` folder for the parity client. All the data (chain data, keys, secret store data, etc.) will be stored here. You can change the host's folder (first part) if you want to allocate in a different host location.
- The volume /etc/parity/ is the configuration folder for the parity client. The parity configuration file (`config.toml`) and the Ocean chain specification (`chain.json`), and the account password (for validators) are located in this folder. Again you can modify the local folder without mahor issue.

3. Add the Parity configuration file `/etc/parity/config.toml`. To understand the settings, see the [docs about configuring Parity Ethereum](https://wiki.parity.io/Configuring-Parity-Ethereum.html) and the [docs about Parity Proof-of-Authority Chains](https://wiki.parity.io/Proof-of-Authority-Chains).

```bash
[parity]
chain = "/etc/parity/chain.json"
base_path = "/parity_data"
no_persistent_txqueue = true

[rpc]
disable = false
port = 8545
interface = "all"
cors = ["all"]
hosts = ["all"]
apis = ["all"]

[secretstore]
disable = true

[network]
nat = "extip:<PUBLIC_IP"
port = 30303
reserved_peers = "/etc/parity/peers"
node_key = "<ETHEREUM_NODE_KEY>"
discovery = true

[ui]
disable = true

[ipc]
disable = true

[dapps]
disable = true

[websockets]
disable = false
port = 8546
interface = "all"
origins = ["all"]
apis = ["all"]
hosts = ["all"]

[mining]
engine_signer = "<ACCOUNT_ADDRESS>"
force_sealing = true
reseal_on_txs = "none" // Authorities reseal automatically. From https://bit.ly/2Jiw5SV
gas_floor_target = "6666666"
usd_per_tx = "0" // Allows for free transactions. From https://bit.ly/2Jiw5SV

[account]
password = ["/etc/parity/password"]

[footprint]
tracing = "on"
pruning = "archive"
fat_db = "on"
```

Where:

- ACCOUNT_ADDRESS: Please put here your authority account address if you have one. Example: `0xdeadbeefcafe0000000000000000000000000001`
- PUBLIC_IP: The external public IP address of your node. Example: `52.1.94.55`
- ETHEREUM_NODE_KEY: The Ethereum node address. Example: `ade848e26c30d600da8207d0f8960d6abf125d1ac432bd42b020a98e10812f36`
- The file `/etc/parity/password` contains the account's password. You can change the location of this file, but take care that this location refers to a location inside the parity container, so you have to refer to the container location and mount the host's file to that location.

4. Get the chain file for the network you're joining and save it in `/etc/parity/chain.json`. Here are some links to chain files:

   - [Nile chain file](https://github.com/oceanprotocol/atlantic/blob/master/networks/nile/chain.json)
   - [Duero chain file](https://github.com/oceanprotocol/atlantic/blob/master/networks/duero/chain.json)

5. Once you have configured your files, you can enable and start the service:

```bash
systemctl enable parity-client.service && systemctl start parity-client.service
```

And check the status of the service:
```bash
systemctl status parity-client.service
```

