# Ocean Parity Installation Guide

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

1. Install Docker CE. Please check Docker official documentation (link) for a detailed guide.

2. Configure the Systemd unit:
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
 --volume=/etc/ocean/:/etc/ocean/\
 --entrypoint=/opt/parity/parity\
 oceanprotocol/parity-ethereum:master\
 --config /etc/ocean/config.toml
ExecStop=/usr/bin/docker rm -f %N

[Install]
WantedBy=default.target
EOF

sudo chmod 644 /etc/systemd/system/parity.service
```

Please note:
- The ports exposed from the container to the host are 30303/tcp (ethereum listener), 30303/udp (ethereum discovery) , 8545 (rpc interface) and 8546 (websocket interface). You can modify the host's ports (the first ports) if you want to modify the exposed ports in the host.
- The volume /parity_data is the `base_path` folder for the parity client. All the data (chain data, keys, secret store data, etc.) will be stored here. You can change the host's folder (first part) if you want to allocate in a different host location.
- The volume /etc/ocean is the configuration folder for the parity client. The parity configuration file (`config.toml`) and the Ocean chain specification (`chain.json`), and the account password (for validators) are located in this folder. Again you can modify the local folder without mahor issue.

3. Add the parity configuration:
```bash
[parity]
chain = "/etc/ocean/chain.json"
base_path = "/parity_data"
no_persistent_txqueue = true

[ui]
disable = true

[rpc]
disable = true
interface = "all"
cors = ["all"]
hosts = ["all"]
apis = ["all"]

[ipc]
disable = true

[websockets]
disable = true
port = 8546
interface = "all"
origins = ["all"]
apis = ["all"]
hosts = ["all"]

[mining]
engine_signer = "<ACCOUNT_ADDRESS>"
reseal_on_txs = "none"
usd_per_tx = "0"
gas_floor_target = "4700000"

[network]
nat = "extip:<PUBLIC_IP>"
port = 30303
node_key = "<ETHEREUM_NODE_KEY>"
discovery = true

[ipfs]
enable = false

[snapshots]
disable_periodic = true

[account]
password = ["/etc/ocean/password"]

[secretstore]
disable = true
```

Where:
- ACCOUNT_ADDRESS: This is the ethereum address where you want to mine. Please put here your authority account if you have one.
- PUBLIC_IP: The external public IP of your node.
- ETHEREUM_NODE_KEY: The Ethereum node address
- The file `/etc/ocean/password` contains the account's password. You can change the location of this file, but take care that this location reffers to a location inside the parity container, so you have to refer to the container location and mount the host's file to that location.

4. Add the Nile chain file to `/etc/ocean/chain.json`:
```json
{
  "name": "nile",
  "engine": {
    "authorityRound": {
      "params": {
        "stepDuration": "5",
        "validators": {
          "list": [
            "0xa1345ed0b2d1e193aead673e33dac56515af128a",
            "0xf4a2dbd5d67ef3019a7e4b188b92128c86d76bf9",
            "0xcdbe5f61fac89e2cfcfa3727805d5cb8bc4f9afd"
          ]
        },
        "validateScoreTransition": 1000000000,
        "validateStepTransition": 1500000000,
        "maximumUncleCount": 1000000000
      }
    }
  },
  "params": {
    "maximumExtraDataSize": "0x20",
    "minGasLimit": "0x1388",
    "networkID": "0x2323",
    "gasLimitBoundDivisor": "0x400",
    "eip140Transition": 0,
    "eip211Transition": 0,
    "eip214Transition": 0,
    "eip658Transition": 0
  },
  "genesis": {
    "seal": {
      "authorityRound": {
        "step": "0x0",
        "signature": "0x0000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000"
      }
    },
    "difficulty": "0x20000",
    "gasLimit": "0x165A0BC00"
  },
  "accounts": {
    "0x0000000000000000000000000000000000000001": {
      "balance": "1",
      "builtin": {
        "name": "ecrecover",
        "pricing": {
          "linear": {
            "base": 3000,
            "word": 0
          }
        }
      }
    },
    "0x0000000000000000000000000000000000000002": {
      "balance": "1",
      "builtin": {
        "name": "sha256",
        "pricing": {
          "linear": {
            "base": 60,
            "word": 12
          }
        }
      }
    },
    "0x0000000000000000000000000000000000000003": {
      "balance": "1",
      "builtin": {
        "name": "ripemd160",
        "pricing": {
          "linear": {
            "base": 600,
            "word": 120
          }
        }
      }
    },
    "0x0000000000000000000000000000000000000004": {
      "balance": "1",
      "builtin": {
        "name": "identity",
        "pricing": {
          "linear": {
            "base": 15,
            "word": 3
          }
        }
      }
    },
    "0xf5d60efcad44babc8d85eb8df57d9e3a5101eb93": {
      "balance": "0x2000000000000000000"
    },
    "0x61f9546cd2bd5e46bdb915ab9c69210c6ee043d2": {
      "balance": "0x2000000000000000000"
    },
    "0x8d968b9bc8fe3b1d233ae6238c771544dd7c8fb2": {
      "balance": "0x2000000000000000000"
    },
    "0x63132a33765fe352187c93e45fac1b9162399e5c": {
      "balance": "0x2000000000000000000"
    },
    "0x95a165da81359c11aafb4490fe6afeb6ee4f7c2e": {
      "balance": "0x2000000000000000000"
    },
    "0x9224dff492427471092d13b36866875d7e0f2dd6": {
      "balance": "0x2000000000000000000"
    },
    "0x9aa9bfa819800a44803dcacc514bb443a2baade6": {
      "balance": "0x2000000000000000000"
    },
    "0x85ef014983c1bad421cc00941c18122066da5860": {
      "balance": "0x2000000000000000000"
    },
    "0xc6fffcb664a60c019a335902112584829a91c0a0": {
      "balance": "0x2000000000000000000"
    },
    "0xdbb63fb51453714f5efed3c2846160018fb3f43c": {
      "balance": "0x2000000000000000000"
    },
    "0x23be64786fe4467635e65aea49f9dd7a0b05d7b5": {
      "balance": "0x2000000000000000000"
    },
    "0x3c80c7163c1e6730f2ba80df707178dcdee44475": {
      "balance": "0x2000000000000000000"
    },
    "0xe49c5f9c8a78884c8ba2aa161b8c41bf21b96751": {
      "balance": "0x2000000000000000000"
    },
    "0xf7f2673e854e7b33a62beec796dec5d61ac9c40a": {
      "balance": "0x2000000000000000000"
    },
    "0xe739ed3643970d106c4b1c51de91b7992400e50c": {
      "balance": "0x2000000000000000000"
    },
    "0x85a7273ff4f4ffce0c708f47ab45249867679bcf": {
      "balance": "0x2000000000000000000"
    },
    "0x4156e7abdfe6e08c16ef2b241661276af57f9ead": {
      "balance": "0x2000000000000000000"
    },
    "0x480b0e0a641ae45521377d4984d085a003934561": {
      "balance": "0x200000000000000000000000"
    },
    "0xb08eb95236499f072b2ae3c91a8f911d6c853b4d": {
      "balance": "0x200000000000000000000000"
    },
    "0x4899f06b5e60140f1885599bf3a562cb25775adc": {
      "balance": "0x200000000000000000000000"
    }
  },
  "nodes": [
    "enode://e90d77ad3065d0767abe08cdd47746c29fae578e8d1c06aacc30da720dd7fd8c8aa0e1e2ca2bb20c4739edff82e5e7405a382cf17af6a4366e2540f72e46e829@18.210.120.241:30303",
    "enode://204b11794d16f382bab24a7cdde5ec1273b8c6c2b23e64560d7ef1707708158a45300e920607aa413633e45c5eea472c17f474866ce5901515249abfbe9c4e1c@35.171.30.2:30303",
    "enode://6881fbbf8f2400c05c61b696af7bb2be04ab22ce64d5221906b9c1c67f2bf976b71cd1d98be7743dfc139a7b590fe230212195714e71f2204252caf68f6f94c0@54.163.9.188:30303",
    "enode://2676315c9bc954a275b949b4b77300b694d9829b120dab36e7f125dc790a754e97e1100173a2f8ebe6390c87539424e71774996d823bc9d65b262258877a1353@52.1.94.55:30303",
    "enode://5ca724b86ae77e84af2a187f209ee84eb47991689cce6088d6f35b7aa8f50c1bfc74fa5d3f9360da733bef6103c09cf3d8dc956637dd6a7fa7fb69797af0e133@54.156.6.164:30303",
    "enode://6ffb46bfefb6ed24d3626a431b367819c01916272ee10caab791ff19b38e9f7ff904566f6ba83fd24591e66cb3d9ac8b24b8be7e157486b5d23ce0c473c628a4@100.24.158.252:30303"
  ]
}
```

5. Once you have configured your files, you can enable and start the service:

```bash
systemctl enable parity-client.service && systemctl start parity-client.service
```

And check the status of the service:
```bash
systemctl status parity-client.service
```

