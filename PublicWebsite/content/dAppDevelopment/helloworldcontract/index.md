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
$cd /eos/contracts/hello

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

