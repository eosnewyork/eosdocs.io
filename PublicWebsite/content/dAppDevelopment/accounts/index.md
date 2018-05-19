---
title: "Accounts"
date: 2018-04-25T16:07:09-04:00
weight: 4
draft: false
---

### 1. Creating a new account {#create}

Accounts need to be created by another account. 

Assuming you followed the instructions in the [wallets](../wallets/) section, you should now have a wallet in an unlocked state which contains 3 key pairs. 

<font color="red">Make sure you're using the public keys you generated, not the two shown below</font>
```
$cleos --wallet-url http://wallet:5555 -u http://server:7777 create account eosio mynewaccount {MyNewAccount owner Public Key} {MyNewAccount active Public Key}

$cleos --wallet-url http://wallet:5555 -u http://server:7777 create account eosio mynewaccount EOS7EzCEh94uN2k59wznzsZDcFVnpZ3wuiYvPSbb8bXDS6U7twKQF EOS5tJQSKKeiTUZEutPo9SWUoCeovV43kWxGuW21K663frcHw7GnN
```

The response should look something like this
```
executed transaction: d4a764ae9c728f9a2c95537613445f059e4833d1cde12504f6d6e88ec10951ab  352 bytes  102400 cycles
#         eosio <= eosio::newaccount            {"creator":"eosio","name":"mynewaccount","owner":{"threshold":1,"keys":[{"key":"EOS6MRyAjQq8ud7hVNYc...
```

{{% notice note %}}
Note that account names must be lower case and should be less than 13 characters. (Only contains the following symbol .12345abcdefghijklmnopqrstuvwxyz). Note that 6,7,8,9,0 are not allowed. 
{{% /notice %}}

### 2. Account Info {#info}

```
$cleos -u http://server:7777 get account mynewaccount -j

{
  "account_name": "mynewaccount",
  "permissions": [{
      "perm_name": "active",
      "parent": "owner",
      "required_auth": {
        "threshold": 1,
        "keys": [{
            "key": "EOS5tJQSKKeiTUZEutPo9SWUoCeovV43kWxGuW21K663frcHw7GnN",
            "weight": 1
          }
        ],
        "accounts": []
      }
    },{
      "perm_name": "owner",
      "parent": "",
      "required_auth": {
        "threshold": 1,
        "keys": [{
            "key": "EOS7EzCEh94uN2k59wznzsZDcFVnpZ3wuiYvPSbb8bXDS6U7twKQF",
            "weight": 1
          }
        ],
        "accounts": []
      }
    }
  ]
}

```

Or get all accounts linked to a given public key

```
$cleos  -u http://server:7777 get accounts EOS7EzCEh94uN2k59wznzsZDcFVnpZ3wuiYvPSbb8bXDS6U7twKQF

{
  "account_names": [
    "mynewaccount",
    "test1"
  ]
}

```
