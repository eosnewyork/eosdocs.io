---
title: "Docker"
date: 2018-04-26T16:12:15-04:00
weight: 1
draft: false
---

* [Getting the docker image]({{<ref "#GettingStarted" >}}) 
* [Create a docker network ]({{<ref "#Network" >}}) 
* [Run the containers ]({{<ref "#Running" >}}) 
* [Testing to see if it's all working]({{<ref "#Testing" >}}) 


{{% notice warning %}}
Note that [EOS Wiki](https://github.com/EOSIO/eos/wiki/Local-Environment#3-docker) has instructions on how to use a docker container to compile the newest version code. This may have it's own problems so we encourage the use of the docker image referenced below while you're learning. This will be easier and faster initially. 
{{% /notice %}}


#### Introduction

If you don't alredy have docker installed, you can download it here: https://www.docker.com/community-edition

#### 1. Getting the docker image {#GettingStarted}

The below statement will download an Ubuntu image which contains the compiled software. 

```
docker pull eosio/eos
```

As a quick test, run the image and gain access to a bash shell, do the following: 

```
docker run --rm -it eosio/eos bash
```

If that works, you should get to a prompt that looks like the following, and typing "cleos" should return the help for the cleos tool:

```
root@a5f9eafaab74:/#cleos
ERROR: RequiredError: Subcommand required
Command Line Interface to EOSIO Client
Usage: cleos [OPTIONS] SUBCOMMAND

Options:
  -h,--help                   Print this help message and exit
  -u,--url TEXT=http://localhost:8888/
                              the http/https URL where nodeos is running
  --wallet-url TEXT=http://localhost:8888/
                              the http/https URL where keosd is running
  -v,--verbose                output verbose actions on error

Subcommands:
  version                     Retrieve version information
  create                      Create various items, on and off the blockchain
  get                         Retrieve various items and information from the blockchain
  set                         Set or update blockchain state
  transfer                    Transfer EOS from account to account
  net                         Interact with local p2p network connections
  wallet                      Interact with local wallet
  sign                        Sign a transaction
  push                        Push arbitrary transactions to the blockchain
  multisig                    Multisig contract commands
  system                      Send eosio.system contract action to the blockchain.
root@a5f9eafaab74:/#
root@a5f9eafaab74:/#exit
```

exit the image by typing "exit"

#### 2. Create a docker network {#Network}

Create a docker network which will allow the containers to communicate with one another

```
docker network create eosnetwork
```


#### 3. Run the containers {#Running}

To run the server software (on port 7777): 

```
docker run --name server --network=eosnetwork --rm -p 7777:7777 -i eosio/eos /bin/bash -c "nodeos -e -p eosio --plugin eosio::producer_plugin --plugin eosio::chain_api_plugin --plugin eosio::http_plugin -d /mnt/dev/data --http-server-address=0.0.0.0:7777 --access-control-allow-origin=* --http-validate-host=0"
```

To run the wallet software (on port 5555): 

```
docker run --name wallet --network=eosnetwork --rm -p 5555:5555 -i eosio/eos /bin/bash -c "keosd --http-server-address=0.0.0.0:5555 --http-validate-host=0"
```

Let's open a bash shell so that we can test some of the tools

```
docker run --name tools --network=eosnetwork --rm -it eosio/eos /bin/bash 
```

#### 4. Testing to see if it's all working {#Testing}

Now let's make sure the server is working:

1. [http://localhost:7777/v1/chain/get_info](http://localhost:7777/v1/chain/get_info) should work in a local web browser
2. Running this command from the tools docker instance should work:

```
$ cleos -u http://server:7777 get info

# Expected response
{
  "server_version": "749a6759",
  "head_block_num": 1953,
  "last_irreversible_block_num": 1952,
  "last_irreversible_block_id": "000007a0c1ae4e28480dcbeef36e9d4970987969f850453dcf8e244b569d6325",
  "head_block_id": "000007a1fc0d5b3dd16ebfe18ab9a288ac8bc7d03caee050a58a502577d25560",
  "head_block_time": "2018-05-16T02:04:08",
  "head_block_producer": "eosio",
  "virtual_block_cpu_limit": 701979,
  "virtual_block_net_limit": 7389096,
  "block_cpu_limit": 99900,
  "block_net_limit": 1048576
}
```

```
$ cleos --wallet-url http://wallet:5555 wallet list keys

# We have not created any wallets yet, so this is the expected response
Wallets:
[]
[]
```



