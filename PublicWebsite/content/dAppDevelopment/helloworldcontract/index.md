---
title: "Hello world contract"
date: 2018-04-24T09:23:39-04:00
weight: 5
draft: false
---

### 1. Creating your 1st contract

https://github.com/EOSIO/eos/wiki/Tutorial-Getting-Started-With-Contracts

-p eosio tells cleos to sign this action with the **active** authority of the eosio account, i.e., to sign the action using the private key for the eosio account that we imported earlier.

```
# If you're using he docker image this is where you'll find the source code for the example contracts. We're going to add another here. 
$cd /eos/contracts/

$mkdir hello

```

In the hello directory create a new file "hello.cpp" with the following contents

```
$vi hello/hello.cpp
```

```
#include <eosiolib/eosio.hpp>
#include <eosiolib/print.hpp>
using namespace eosio;

class hello : public eosio::contract {
 public:
     using contract::contract;

     /// @abi action
     void hi( account_name user ) {
             print( "Hello, ", name{user} );
     }
};

EOSIO_ABI( hello, (hi) )

```

Now let's compile the C++ into web assembly (.wast file)

```
$eosiocpp -o /eos/contracts/hello/hello.wast /eos/contracts/hello/hello.cpp

# The output will look something like this...
In file included from /eos/contracts/hello/hello.cpp:5:
In file included from /eos/contracts/hello/hello.hpp:5:
In file included from /usr/local/include/eosiolib/eosio.hpp:7:
In file included from /usr/local/include/eosiolib/action.hpp:7:
In file included from /usr/local/include/eosiolib/datastream.hpp:9:
....
....
5 warnings generated.  


# You can ignore these warnings

```

Generate an abi file

```
eosiocpp -g /eos/contracts/hello/hello.abi /eos/contracts/hello/hello.cpp
```

Check you should now have a .wast file and a .abi file in the folder. 

```
$ls -1 /eos/contracts/hello/

hello.abi
hello.cpp
hello.wast
```


### 2. Upload your new contract to your account

```
$cleos --wallet-url http://wallet:5555 -u http://server:7777 set contract mynewaccount /eos/contracts/hello/ -p mynewaccount

Reading WAST/WASM from /eos/contracts/hello/hello.wast...
Assembling WASM...
Publishing contract...
executed transaction: c15e40773b43e98022022c808ee013c0acd5c758ff3ebab5f9e64e98d2c07540  1648 bytes  2200576 cycles
#         eosio <= eosio::setcode               {"account":"mynewaccount","vmtype":0,"vmversion":0,"code":"0061736d0100000001370b60027f7e0060027e7e0...
#         eosio <= eosio::setabi                {"account":"mynewaccount","abi":{"types":[],"structs":[{"name":"hi","base":"","fields":[{"name":"use...
```

```
$cleos --wallet-url http://wallet:5555 -u http://server:7777 push action mynewaccount hi '["username1"]' --permission mynewaccount@active

executed transaction: 259f32454f3b075cc0eb02557e858da9dcce39d88b833bda62a0ac51ba5e340c  232 bytes  102400 cycles
#  mynewaccount <= mynewaccount::hi             {"user":"username1"}
>> Hello, username1
```

Create a user called "Bob" and then issue some tokens to Bob

```
./cleos create key
(bob owner) Private key: 5HuaTWKeGzZhqyzuzFAjjFjPnnnjdgcp562oBSS8Wv1qgDSkR2W
(bob owner) Public key: EOS89EDpYUcbrZauAcvAN78EbMzK4kfC7shhuTH4Dr2A3ZYanyCax

./cleos create key
(bob active) Private key: 5JbnQ1WpnSECgJWkDtN7vCReayA9yRZ1pBeYBmcUHxC3AAaNkoP
(bob active) Public key: EOS5kzfmXMR9TJ5VM2tSD3Wzmwm9Q4KWkLMGWD3FjGjfJLgS5NxPU

```

```
$./cleos wallet create on bobwallet
$./cleos wallet import 5HuaTWKeGzZhqyzuzFAjjFjPnnnjdgcp562oBSS8Wv1qgDSkR2W -n bobwallet
$./cleos wallet import 5JbnQ1WpnSECgJWkDtN7vCReayA9yRZ1pBeYBmcUHxC3AAaNkoP -n bobwallet
```

```
$./cleos -H ec2-34-238-254-245.compute-1.amazonaws.com -p 8888 create account eosio bob EOS89EDpYUcbrZauAcvAN78EbMzK4kfC7shhuTH4Dr2A3ZYanyCax EOS5kzfmXMR9TJ5VM2tSD3Wzmwm9Q4KWkLMGWD3FjGjfJLgS5NxPU
```

```
$./cleos  -H ec2-34-238-254-245.compute-1.amazonaws.com -p 8888 push action eosio.token issue '[ "bob", "100.0000 EOS", "memo" ]' -p eosio

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
./cleos create key
(bob owner) Private key: 5J3TqwGXBfTvWNfZTQ6rwgwtpCJQyU7HXJtAdY32XtQbe8T6Yfe
(bob owner) Public key: EOS6drFacd98ETXoXQfa7dmUXfqcntivCaD6A75XDxvRaphiNUwdg

./cleos create key
(bob active) Private key: 5JqsxPm6FmQZ5nXHgMiN4UfWag8QoN2Vkdff8yQAcbfijR1Mexz
(bob active) Public key: EOS4xdhFNwZBGGLoWMrYZ9QqVmzt6ewRqvauW5LDJxmddQGrk3wMR
```

```
$./cleos wallet create on marywallet
$./cleos wallet import 5J3TqwGXBfTvWNfZTQ6rwgwtpCJQyU7HXJtAdY32XtQbe8T6Yfe -n marywallet
$./cleos wallet import 5JqsxPm6FmQZ5nXHgMiN4UfWag8QoN2Vkdff8yQAcbfijR1Mexz -n marywallet
```

```
$./cleos -H ec2-34-238-254-245.compute-1.amazonaws.com -p 8888 create account eosio mary EOS6drFacd98ETXoXQfa7dmUXfqcntivCaD6A75XDxvRaphiNUwdg EOS4xdhFNwZBGGLoWMrYZ9QqVmzt6ewRqvauW5LDJxmddQGrk3wMR
```

```
$./cleos -H ec2-34-238-254-245.compute-1.amazonaws.com -p 8888 push action eosio.token transfer '[ "bob", "mary", "25.0000 EOS", "m" ]' -p bob

executed transaction: 6b2fb84d9e5c9dd13b576ea24ae8d4a244a60a889e601e540369ec179d991df0  256 bytes  111616 cycles
#   eosio.token <= eosio.token::transfer        {"from":"bob","to":"mary","quantity":"25.0000 EOS","memo":"m"}
>> transfer
#           bob <= eosio.token::transfer        {"from":"bob","to":"mary","quantity":"25.0000 EOS","memo":"m"}
#          mary <= eosio.token::transfer        {"from":"bob","to":"mary","quantity":"25.0000 EOS","memo":"m"}

```


### 3. Errors

```
./cleos -H ec2-34-238-254-245.compute-1.amazonaws.com -p 8888 push action eosio.token transfer '[ "bob", "mary", "25.0000 EOS", "m" ]' -p bob
Error 3140006: No available wallet
Ensure that you have created a wallet and have it open
Error Details:
You don't have any wallet!

./cleos wallet open
Opened: default
./cleos -H ec2-34-238-254-245.compute-1.amazonaws.com -p 8888 push action eosio.token transfer '[ "bob", "mary", "25.0000 EOS", "m" ]' -p bob
Error 3140003: Locked wallet
Ensure that your wallet is unlocked before using it!
Error Details:
You don't have any unlocked wallet!

./cleos wallet unlock
password: Unlocked: default
./cleos -H ec2-34-238-254-245.compute-1.amazonaws.com -p 8888 push action eosio.token transfer '[ "bob", "mary", "25.0000 EOS", "m" ]' -p bob
Error 3030002: signatures do not satisfy declared authorizations
Ensure that you have the related private keys inside your wallet and your wallet is unlocked.
Error Details:
transaction declares authority '{"actor":"bob","permission":"active"}', but does not have signatures for it.

./cleos wallet unlock -n bobwallet
password: Unlocked: bobwallet
./cleos -H ec2-34-238-254-245.compute-1.amazonaws.com -p 8888 push action eosio.token transfer '[ "bob", "mary", "25.0000 EOS", "m" ]' -p bob
executed transaction: 5311793542d5a7938e4089f641db11ebc97efd02331fa7f3756e1499f7a73d93  256 bytes  111616 cycles
#   eosio.token <= eosio.token::transfer        {"from":"bob","to":"mary","quantity":"25.0000 EOS","memo":"m"}
>> transfer
#           bob <= eosio.token::transfer        {"from":"bob","to":"mary","quantity":"25.0000 EOS","memo":"m"}
#          mary <= eosio.token::transfer        {"from":"bob","to":"mary","quantity":"25.0000 EOS","memo":"m"}
```