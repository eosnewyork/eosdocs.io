---
title: "Accounts"
date: 2018-04-25T16:07:09-04:00
weight: 4
draft: false
---

### 1. Creating a new account {#create}

Accounts need to be created by another account. 

Assuming you followed the instructions in the [wallets](../wallets/) section, you should now have a wallet in an unlocked state which contains 3 key pairs. 

```
# ./cleos -H ec2-35-171-26-29.compute-1.amazonaws.com -p 8888 create account eosio mynewaccount {MyNewAccount owner Public Key} {MyNewAccount active Public Key}

./cleos -H ec2-35-171-26-29.compute-1.amazonaws.com -p 8888 create account eosio mynewaccount EOS6MRyAjQq8ud7hVNYcfnVPJqcVpscN5So8BhtHuGYqET5GDW5CV EOS7EzCEh94uN2k59wznzsZDcFVnpZ3wuiYvPSbb8bXDS6U7twKQF
```

The response should look something like this
```
executed transaction: d4a764ae9c728f9a2c95537613445f059e4833d1cde12504f6d6e88ec10951ab  352 bytes  102400 cycles
#         eosio <= eosio::newaccount            {"creator":"eosio","name":"mynewaccount","owner":{"threshold":1,"keys":[{"key":"EOS6MRyAjQq8ud7hVNYc...
```

{{% notice note %}}
Note that account names must be lower case and should be less than 13 characters. (Only contains the following symbol .12345abcdefghijklmnopqrstuvwxyz)
{{% /notice %}}

### 2. Account Info {#info}

```
$./cleos -H ec2-34-238-254-245.compute-1.amazonaws.com -p 8888 get account myaccount

{
  "account_name": "myaccount",
  "permissions": [{
      "perm_name": "active",
      "parent": "owner",
      "required_auth": {
        "threshold": 1,
        "keys": [{
            "key": "EOS6YrDSuwb6M2uMpCoJo93u3WJyjQdi7kvK33A8jLfoLQ7rBnCcK",
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
            "key": "EOS8ThCWum1k1YbLEmsr8kGrBYXbAAaVfvajHpC98spS3rc83Nizb",
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
./cleos -H ec2-34-238-254-245.compute-1.amazonaws.com -p 8888 get accounts EOS7EzCEh94uN2k59wznzsZDcFVnpZ3wuiYvPSbb8bXDS6U7twKQF

{
  "account_names": [
    "mynewaccount",
    "test1"
  ]
}

```