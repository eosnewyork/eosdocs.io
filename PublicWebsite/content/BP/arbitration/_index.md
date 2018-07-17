---
title: "Arbitration"
date: 2018-07-14T09:07:37-04:00
pre: "<b>3. </b>"
draft: false
weight: 5
---

In the event of an an Arbitration order, the following process should be followed: 

## Notification 

At time of writing (2018-07-14) ECAF does two things:
1. Send a message on chain 
2. Notifies BPs by sending a message in the #arbitration Keybase channel (sometimes a notice will be posted in #general telling people to check #arbitration).

Example message in the #arbitration channel :
```
==========================
Following is AN ACTIVE Order.

BP Actions Requested
=========================
DATE: 2018-07-13
SUMMARY: Temporary Freeze Order
DETAILS: An Arbitral Order AO-003 has been issued on the EOS MainNet. The transaction ID of the EOS message, issued from ECAF's official EOS account, and showing the location of the file and its hash is:
​
https://eosflare.io/tx/280bd6b813e60bb23ec9f7521c09b754a64b0d5ac4a5bac3fa84233be3c6a41c
​
Block Producers are requested to verify the file and take the appropriate actions.
​
Moti Tabulo, as ECAF Interim Administrator
```

The link is to transaction (Example: https://eosflare.io/tx/280bd6b813e60bb23ec9f7521c09b754a64b0d5ac4a5bac3fa84233be3c6a41c )which looks as follows:

```

{"msg":"This is NOT A TEST. https://eoscorearbitration.io/wp-content/uploads/2018/07/ECAF-Temporary-Freeze-Order-2018-07-13-AO-003.pdf (SHA3-256 hash: ca104c57af040b5b46ab6fb2bcb8455ed8f81402e5e586d8a50a47cfc2683a20)"}
```

## Check the source of the message

The message should have been sent from / signed by "ecafofficial". So looking at the transactions for "ecafofficial" account should show the same message:  

https://www.bloks.io/account/ecafofficial   

Or you can use cleos to do the same thing:

```
cleos -u https://api.eosnewyork.io get actions ecafofficial --full
#  seq  when                              contract::action => receiver      trx id...   args
================================================================================================================
#    0   2018-06-27T00:05:16.500         eosio::updateauth => eosio         70b510b8...
{"account":"ecafofficial","permission":"active","parent":"owner","auth":{"threshold":1,"keys":[{"key":"EOS8P6dTuxPUWyvXoe9Tn1QG5QocxL8vMLoCq4J42p2KuX3e2g2vs","weight":1}],"accounts":[],"waits":[]}}
#    1   2018-06-27T00:05:41.500         eosio::updateauth => eosio         99ca1046...
{"account":"ecafofficial","permission":"owner","parent":"","auth":{"threshold":1,"keys":[{"key":"EOS8P6dTuxPUWyvXoe9Tn1QG5QocxL8vMLoCq4J42p2KuX3e2g2vs","weight":1}],"accounts":[],"waits":[]}}
#    2   2018-07-12T19:57:12.000         eosio::updateauth => eosio         e0f131e0...
{"account":"ecafofficial","permission":"active","parent":"owner","auth":{"threshold":1,"keys":[{"key":"EOS4v8eF1V6huuYrBvubWsQumS1TkF2SqzkLsAv9i3LkPLXd6V2rs","weight":1}],"accounts":[],"waits":[]}}
#    3   2018-07-13T18:12:16.500       decentwitter::tweet => decentwitter  1f45361f...
{"msg":"TEST! https://eoscorearbitration.io/wp-content/uploads/2018/07/ECAF_Arbitrator_Order_2018-06-19-AO-001.pdf (SHA3-256 hash: a80df3e8cfa895a02161dc4d5d04392e3274bce917935c6c214cfe0f1f7e868a)"}
#    4   2018-07-13T21:49:31.000       decentwitter::tweet => decentwitter  280bd6b8...
{"msg":"This is NOT A TEST. https://eoscorearbitration.io/wp-content/uploads/2018/07/ECAF-Temporary-Freeze-Order-2018-07-13-AO-003.pdf (SHA3-256 hash: ca104c57af040b5b46ab6fb2bcb8455ed8f81402e5e586d8a50a47cfc2683a20)"}
```


### Check that the hash of the file matches 

The message on chain should contain a SHA3-256 hash - in this example: ca104c57af040b5b46ab6fb2bcb8455ed8f81402e5e586d8a50a47cfc2683a20

Download the file and check the hash

```
$apt install rhash

$wget https://eoscorearbitration.io/wp-content/uploads/2018/07/ECAF-Temporary-Freeze-Order-2018-07-13-AO-003.pdf

rhash --sha3-256 ECAF-Temporary-Freeze-Order-2018-07-13-AO-003.pdf

ca104c57af040b5b46ab6fb2bcb8455ed8f81402e5e586d8a50a47cfc2683a20  ECAF-Temporary-Freeze-Order-2018-07-13-AO-003.pdf
```

## Apply the change to your BP node(s)

In the example ECAF order, the following accounts were listed so we're going to add them as entries to the config file. 

```
neverlandwal
tseol5n52kmo
potus1111111
```

The BP config file should contain a number of records that look as follows: 

```
actor-blacklist = blacklistmee
actor-blacklist = ge2dmmrqgene
actor-blacklist = gu2timbsguge
actor-blacklist = ge4tsmzvgege
actor-blacklist = gezdonzygage
...
...
...
actor-blacklist = gu2teobyg4ge
actor-blacklist = gu4damztgyge
actor-blacklist = ha4doojzgyge
actor-blacklist = neverlandwal
actor-blacklist = tseol5n52kmo
actor-blacklist = potus1111111
```

To confirm that you have not made any typo's run the following command and share the output in the #arbitration channel. 

```
$grep actor-black config.ini | grep -v "#" | sort | tr -d " " | sha256sum
d2acb47d52615e316f89b04d397852daae4137bd0355768c8876783885d6cbd6
```

The other BPs will also be sharing their hash and you're should match. 




#### References 

https://steemit.com/eos/@ecaf/draft-proposal-for-ecaf-s-official-notification-system
