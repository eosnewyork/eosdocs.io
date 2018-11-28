---
title: "API endpoints"
date: 2018-04-30T15:53:46-04:00
draft: false
weight: 1
---

### Public API Endpoints Mainnet 
#### Chain_id: aca376f206b8fc25a6ed44dbdc66547c36c6c33e3a119ffbeaef943642f0e906

Every block producer publishes a bp.json file which among other things lists their api endpoints (if they've chosen to provide one). The steps are: 

- find the producer's public url (see http://eosnetworkmonitor.io/ or https://eostracker.io/producers)
- append `/bp.json` to it
- look for the `api_endpoint` key - the corresponding value is the API endpoint. 
- for example for eosnewyork, you would use: http://bp.eosnewyork.io/bp.json 

That said below is a quick reference to some public API endpoints that can be used to vote / interact with the production EOS blockchain. 

* `https://api.eosnewyork.io`
* `https://api.eosio.cr:80`
* `https://api.eosdetroit.io:443`
* `https://eos.greymass.com:443`
* `https://api.eosmetal.io:18890`
* `http://api.hkeos.com:80`
* `https://eosapi.blockmatrix.network:443`
* `https://fn.eossweden.se:443`
* `http://api.blockgenicbp.com:8888`
* `http://mainnet.eoscalgary.io:80`
* `https://node1.eosphere.io` and `https://node2.eosphere.io`
* `https://eos.saltblock.io`
* `http://eos-api.worbli.io:80`
* `https://eos-api.worbli.io:443`
* `http://mainnet.eoscalgary.io:80`
* `https://user-api.eoseoul.io:443` and `http://user-api.eoseoul.io:80` with CORS supported
* `https://node2.liquideos.com:8883` and `http://node2.liquideos.com:8888`
* `https://api.eosuk.io:443`
* `http://api1.eosdublin.io:80`
* `http://api.eosvibes.io:80`
* `http://api.cypherglass.com:8888` and `https://api.cypherglass.com:443`
* `http://bp.cryptolions.io:8888`
* `http://dc1.eosemerge.io` and `https://dc1.eosemerge.io`
* `https://api.eosio.cr:443`
* `https://api.eosn.io`
* `https://eu1.eosdac.io:443`
* `https://api.main.alohaeos.com:443`
* `https://rpc.eosys.io`

#### Usage Note for cleos users

To quickly test one of these apis run: 

`cleos -u <url> get info`

for example: 

`cleos -u http://api.eosnewyork.io:80 get info`

You should see a response with the most recent block information, the producer's name, timestamp etc. 

Please note that to perform voting you will need to interact with the `system` subcommand on the endpoint you are accessing. Some of the endpoints above may be running behind firewalls with some functionality (namely the `system` command) disabled. 

To check if the endpoint you've selected will work for voting, run: 

`cleos -u http://api.eosnewyork.io:80 system listproducers`

If you get back a list of producers then you should be ok. 
### Testnet API Endpoints

* `https://jungle.eosio.cr:443`


### Run your own endpoint 

While this is more complex, running your own node give you full control. 

On a fresh Ubuntu 16 server (use your server of choice of course, but these instructions were executed on that OS) - Note that you need at least 16GB or ram and at minimum 20GB of disk space before you can begin this process. Also note that this take a long time to run, expect 24 hours before your node is in a usable state. 

Clone the repo and compile the software

```
git clone https://github.com/EOSIO/eos.git --recursive
cd eos/
./eosio_build.sh
cd build/
sudo make install
```

Get a copy of the blocks and prepare the env.

A recent copy of the blockchain data can be found at https://eosnode.tools/blocks (Provided by Block Matrix - https://blockmatrix.network/)

```
sudo su 
~~Not best practice but the following performed as root~~
mkdir /eos
md /eos
mkdir data
wget $(wget --quiet "https://eosnode.tools/api/blocks?limit=1" -O- | jq -r '.data[0].s3') -O blocks_backup.tar.gz
tar xvzf blocks_backup.tar.gz
mv mnt/blocks /eos/data
```

Add config.ini to the /eos folder. 

{We plan to provide a suggested config file here in the future to help those not familiar with Node configuration, in the meantime please reach out to EOS New York and request assistance}

```
/usr/local/eosio/bin/nodeos -d /eos/data --config-dir=/oes --hard-replay --wasm-runtime wabt
.... wait ... then wait some more ... it can take many hours for the replay to finish at which point the node will being syning with the network. 
```
