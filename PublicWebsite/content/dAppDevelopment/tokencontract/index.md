---
title: "Token contract"
date: 2018-04-24T09:23:39-04:00
weight: 6
draft: false
---

### 1. Loading the Bios Contract

https://github.com/EOSIO/eos/wiki/Tutorial-Getting-Started-With-Contracts

-p eosio tells cleos to sign this action with the **active** authority of the eosio account, i.e., to sign the action using the private key for the eosio account that we imported earlier.

> If you're using the docker image `/contracts` is the folder where all the contracts are located.

```
$ cleos set contract eosio eosio.bios -p eosio
```

`cleos` calls `eosio::setcode` and `eosio::setabi` method

```
Reading WAST/WASM from /tmp/build/contracts/eosio.bios/eosio.bios.wast...
Assembling WASM...
Publishing contract...
executed transaction: eb4593ca925673d8115f11a7c28bf106b32673a5d725259c8dd6edb533135f48  3288 bytes  2200576 cycles
#         eosio <= eosio::setcode               {"account":"eosio","vmtype":0,"vmversion":0,"code":"0061736d0100000001581060037f7e7f0060057f7e7e7e7e...
#         eosio <= eosio::setabi                {"account":"eosio","abi":{"types":[],"structs":[{"name":"set_account_limits","base":"","fields":[{"n...
```


### 2. Create the eosio.token account and contract. 

```
$ cleos create key
(eosio.token owner) Private key: 5JiYtMBX4PEHyN22kj1v3nxTV8LvchhLXpiorRvWGo7BCd4pLzF
(eosio.token owner) Public key: EOS7HcuW6uqhYSwhhoPi81XpjRPNzkfcU63CYtk95qyY65EVGEK1y

$ cleos create key
(eosio.token active) Private key: 5HyXKjB2sVXqahyLnndRdSL7cpBH32P4SEdqmbCqk8QySv3XYvH
(eosio.token active) Public key: EOS5qi7Fo9CdPXhtdcw6HNL7UMzmASdxJotmCATbhXwCqiFJxTX3o
```

```
$ cleos wallet create -n eosio.token
$ cleos wallet import 5JiYtMBX4PEHyN22kj1v3nxTV8LvchhLXpiorRvWGo7BCd4pLzF -n eosio.token
$ cleos wallet import 5HyXKjB2sVXqahyLnndRdSL7cpBH32P4SEdqmbCqk8QySv3XYvH -n eosio.token
```

```
$ cleos create account eosio eosio.token EOS7HcuW6uqhYSwhhoPi81XpjRPNzkfcU63CYtk95qyY65EVGEK1y EOS5qi7Fo9CdPXhtdcw6HNL7UMzmASdxJotmCATbhXwCqiFJxTX3o

executed transaction: 6df396430290062b90fafec716033028f54a8593b2e735b3469a60d91f81f0b2  352 bytes  102400 cycles
#         eosio <= eosio::newaccount            {"creator":"eosio","name":"eosio.token","owner":{"threshold":1,"keys":[{"key":"EOS7HcuW6uqhYSwhhoPi8...

```

```
$ cleos set contract eosio.token eosio.token -p eosio.token

Reading WAST/WASM from /root/dev/eos/build/contracts/eosio.token/eosio.token.wast...
Assembling WASM...
Publishing contract...
executed transaction: cde524041bffd65868d7e35e11b8a189ab3742a0f827dd035bf9254f35098367  8376 bytes  2200576 cycles
#         eosio <= eosio::setcode               {"account":"eosio.token","vmtype":0,"vmversion":0,"code":"0061736d010000000181011560067f7e7f7f7f7f00...
#         eosio <= eosio::setabi                {"account":"eosio.token","abi":{"types":[],"structs":[{"name":"transfer","base":"","fields":[{"name"...
```

```
$ cleos push action eosio.token create '["eosio","1000000000.0000 EOS",0,0,0]' -p eosio.token

executed transaction: 1f06701e85926abbd59a5c236449c27720465b33c62cd09108b8f124823b35f9  248 bytes  104448 cycles
#   eosio.token <= eosio.token::create          {"issuer":"eosio","maximum_supply":"1000000000.0000 EOS","can_freeze":0,"can_recall":0,"can_whitelis...
```

Create a user called "Bob" and then issue some tokens to Bob

```
$ cleos create key
(bob owner) Private key: 5HuaTWKeGzZhqyzuzFAjjFjPnnnjdgcp562oBSS8Wv1qgDSkR2W
(bob owner) Public key: EOS89EDpYUcbrZauAcvAN78EbMzK4kfC7shhuTH4Dr2A3ZYanyCax

$ cleos create key
(bob active) Private key: 5JbnQ1WpnSECgJWkDtN7vCReayA9yRZ1pBeYBmcUHxC3AAaNkoP
(bob active) Public key: EOS5kzfmXMR9TJ5VM2tSD3Wzmwm9Q4KWkLMGWD3FjGjfJLgS5NxPU

```

```
$ cleos wallet create on bobwallet
$ cleos wallet import 5HuaTWKeGzZhqyzuzFAjjFjPnnnjdgcp562oBSS8Wv1qgDSkR2W -n bobwallet
$ cleos wallet import 5JbnQ1WpnSECgJWkDtN7vCReayA9yRZ1pBeYBmcUHxC3AAaNkoP -n bobwallet
```

```
$ cleos create account eosio bob EOS89EDpYUcbrZauAcvAN78EbMzK4kfC7shhuTH4Dr2A3ZYanyCax EOS5kzfmXMR9TJ5VM2tSD3Wzmwm9Q4KWkLMGWD3FjGjfJLgS5NxPU
```

```
$ cleos push action eosio.token issue '[ "bob", "100.0000 EOS", "memo" ]' -p eosio

executed transaction: 2164af30bbfce8ab8b0bf2389a76b80aa3d9dbfef55f81749eb21d8ee696c75f  256 bytes  126976 cycles
#   eosio.token <= eosio.token::issue           {"to":"bob","quantity":"100.0000 EOS","memo":"memo"}
>> issue
#   eosio.token <= eosio.token::transfer        {"from":"eosio","to":"bob","quantity":"100.0000 EOS","memo":"memo"}
>> transfer
#         eosio <= eosio.token::transfer        {"from":"eosio","to":"bob","quantity":"100.0000 EOS","memo":"memo"}
#           bob <= eosio.token::transfer        {"from":"eosio","to":"bob","quantity":"100.0000 EOS","memo":"memo"}
```

Create a user called "Mary" and then have Bob transfer some of his tokens to Mary. 

```
$ cleos create key
(bob owner) Private key: 5J3TqwGXBfTvWNfZTQ6rwgwtpCJQyU7HXJtAdY32XtQbe8T6Yfe
(bob owner) Public key: EOS6drFacd98ETXoXQfa7dmUXfqcntivCaD6A75XDxvRaphiNUwdg

$ cleos create key
(bob active) Private key: 5JqsxPm6FmQZ5nXHgMiN4UfWag8QoN2Vkdff8yQAcbfijR1Mexz
(bob active) Public key: EOS4xdhFNwZBGGLoWMrYZ9QqVmzt6ewRqvauW5LDJxmddQGrk3wMR
```

```
$ cleos wallet create on marywallet
$ cleos wallet import 5J3TqwGXBfTvWNfZTQ6rwgwtpCJQyU7HXJtAdY32XtQbe8T6Yfe -n marywallet
$ cleos wallet import 5JqsxPm6FmQZ5nXHgMiN4UfWag8QoN2Vkdff8yQAcbfijR1Mexz -n marywallet
```

```
$ cleos create account eosio mary EOS6drFacd98ETXoXQfa7dmUXfqcntivCaD6A75XDxvRaphiNUwdg EOS4xdhFNwZBGGLoWMrYZ9QqVmzt6ewRqvauW5LDJxmddQGrk3wMR
```

```
$ cleos push action eosio.token transfer '[ "bob", "mary", "25.0000 EOS", "m" ]' -p bob

executed transaction: 6b2fb84d9e5c9dd13b576ea24ae8d4a244a60a889e601e540369ec179d991df0  256 bytes  111616 cycles
#   eosio.token <= eosio.token::transfer        {"from":"bob","to":"mary","quantity":"25.0000 EOS","memo":"m"}
>> transfer
#           bob <= eosio.token::transfer        {"from":"bob","to":"mary","quantity":"25.0000 EOS","memo":"m"}
#          mary <= eosio.token::transfer        {"from":"bob","to":"mary","quantity":"25.0000 EOS","memo":"m"}

```


### 3. Errors

```
$ cleos push action eosio.token transfer '[ "bob", "mary", "25.0000 EOS", "m" ]' -p bob
Error 3140006: No available wallet
Ensure that you have created a wallet and have it open
Error Details:
You don't have any wallet!

$ cleos wallet open
Opened: default
$ cleos -H ec2-34-238-254-245.compute-1.amazonaws.com -p 8888 push action eosio.token transfer '[ "bob", "mary", "25.0000 EOS", "m" ]' -p bob
Error 3140003: Locked wallet
Ensure that your wallet is unlocked before using it!
Error Details:
You don't have any unlocked wallet!

$ cleos wallet unlock
password: Unlocked: default

$ cleos push action eosio.token transfer '[ "bob", "mary", "25.0000 EOS", "m" ]' -p bob
Error 3030002: signatures do not satisfy declared authorizations
Ensure that you have the related private keys inside your wallet and your wallet is unlocked.
Error Details:
transaction declares authority '{"actor":"bob","permission":"active"}', but does not have signatures for it.

$ cleos wallet unlock -n bobwallet
password: Unlocked: bobwallet

$ cleos push action eosio.token transfer '[ "bob", "mary", "25.0000 EOS", "m" ]' -p bob
executed transaction: 5311793542d5a7938e4089f641db11ebc97efd02331fa7f3756e1499f7a73d93  256 bytes  111616 cycles
#   eosio.token <= eosio.token::transfer        {"from":"bob","to":"mary","quantity":"25.0000 EOS","memo":"m"}
>> transfer
#           bob <= eosio.token::transfer        {"from":"bob","to":"mary","quantity":"25.0000 EOS","memo":"m"}
#          mary <= eosio.token::transfer        {"from":"bob","to":"mary","quantity":"25.0000 EOS","memo":"m"}
```
