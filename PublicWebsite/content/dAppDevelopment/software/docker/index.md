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
docker pull binaryfocus/eosdawn-2018-04-27-alpha-dev
```

As a quick test, run the image and gain access to a bash shell, do the following: 

```
docker run --rm -it binaryfocus/eosdawn-2018-04-27-alpha-dev bash
```

If that works you should get to a prompt that looks as following and typing "cleos" should return the help for the cleos tool:

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

Create a docker network that will allow the container to communicate with one another

```
docker network create eosnetwork
```


#### 3. Run the containers {#Running}

To run the server software (on port 7777): 

```
docker run --name server --network=eosnetwork --rm -p 7777:7777 -i binaryfocus/eosdawn-2018-04-27-alpha-dev /bin/bash -c "nodeos -e -p eosio --plugin eosio::producer_plugin --plugin eosio::chain_api_plugin --plugin eosio::http_plugin -d /mnt/dev/data --http-server-address=0.0.0.0:7777 --access-control-allow-origin=*"
```

To run the wallet software (on port 5555): 

```
docker run --name wallet --network=eosnetwork --rm -p 5555:5555 -i binaryfocus/eosdawn-2018-04-27-alpha-dev /bin/bash -c "keosd --http-server-address=0.0.0.0:5555"
```

Let's open a bash shell so that we can test some of the tools

```
docker run --name tools --network=eosnetwork --rm -it binaryfocus/eosdawn-2018-04-27-alpha-dev /bin/bash 
```

#### 4. Testing to see if it's all working {#Testing}

Now let's make sure ther server is working:

1. [http://localhost:7777/v1/chain/get_info](http://localhost:7777/v1/chain/get_info) should work in a local web browser
2. Running this command from the tools docker instance should work:

```
$ cleos -u http://server:7777 get info

# Expected response
{
  "server_version": "45fb9218",
  "head_block_num": 98,
  "last_irreversible_block_num": 97,
  "head_block_id": "0000006227c02a11a1c4502e9291340e39bc0a22121c6d9313c83edc44f41c38",
  "head_block_time": "2018-05-10T01:36:16",
  "head_block_producer": "eosio"
}
```

```
$ cleos --wallet-url http://wallet:5555 wallet list keys

# We have not created any wallets yet, so this is the expected response
Wallets:
[]
[]
```



