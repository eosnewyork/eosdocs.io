---
title: "API endpoints"
date: 2018-04-30T15:53:46-04:00
draft: false
weight: 1
---

### Public API Endpoints Mainnet 
#### Chain_id: aca376f206b8fc25a6ed44dbdc66547c36c6c33e3a119ffbeaef943642f0e906
These are public API endpoints that can be used to vote / interact with the production EOS blockchain. 

* `https://api.eosnewyork.io:443`
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
* `http://dc1.eosemerge.io:8888` and `https://dc1.eosemerge.io:5443`
* `https://api.eosio.cr:443`
* `https://api.eosn.io`
* `https://eu1.eosdac.io:443`

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

Coming Soon
