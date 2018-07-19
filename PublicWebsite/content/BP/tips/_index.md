---
title: "BP Tips"
date: 2018-06-11T09:07:37-04:00
pre: "<b>2. </b>"
draft: false
weight: 2
---

### In the event of a problem - fastest method to having a backup BP take over

In the event that a BP in the top 21 experiences a failure, the way to allow a backup BP to take over is to simply to call unregprod

```
cleos -u http://api.eosnewyork.io system unregprod {account}
```

Once the issue has been resolved, call regproducer again and things will resume as before. 


### Setting Up a Claim Rewards Permission

{{% notice warning %}}
DO NOT USE THE PUBLIC KEY GIVEN IT IS THE TUTORIAL KEY AND THE PRIVATE KEY IS WELL KNOWN
{{% /notice %}}


```
---------- Step 1. Create custom permission on an account ----------
#cleos set account permission YOURACCOUNT claim CLAIM_PUBLIC_KEY active -p YOURACCOUNT@active
cleos set account permission eosnewyorkio claim EOS6MRyAjQq8ud7hVNYcfnVPJqcVpscN5So8BhtHuGYqET5GDW5CV active -p eosnewyorkio@active

---------- Step 2. Link action to permission ----------
#cleos set action permission YOURACCOUNT eosio claimrewards claim -p YOURACCOUNT@active
cleos set action permission eosnewyorkio eosio claimrewards claim -p eosnewyorkio@active

---------- Claiming the rewards ----------
# cleos push action eosio claimrewards '{"owner":"<ACCOUNT>"}' -p <ACCOUNT>@claim
cleos push action eosio claimrewards '{"owner":"eosnewyorkio"}' -p eosnewyorkio@claim

---------- Removing the permission ----------
# You must remove the action first before removing the permission

#cleos set action permission YOURACCOUNT eosio claimrewards NULL -p YOURACCOUNT@active
cleos set action permission eosnewyorkio eosio claimrewards NULL -p eosnewyorkio@active

#cleos set account permission YOURACCOUNT claim NULL active -p YOURACCOUNT@active
cleos set account permission eosnewyorkio claim NULL active -p eosnewyorkio@active
```

### Validating System Contract Update

Special thanks to [elmato](https://gist.github.com/elmato/8423b697b94dc0e816a10e6a33dfd9f2)

#### Prerequisites
1. The user is familiar with building EOS and has built the version of the software containing the contract being upgraded
2. `jq` is installed 
```
sudo apt install jq
``` 

#### Check sha256 Results
{{% notice warning %}}
Different OS versions will produce different sha256 results
{{% /notice %}}

1. Check local built version
```
sha256sum build/contracts/eosio.system/eosio.system.wasm
```

2. Check proposed contract 
```
# cleos multisig review <proposer> <proposal_name> | jq -r '.transaction.actions[0].data.code' | xxd -r -p | sha256sum
cleos multisig review eoscandacom onezeroseven | jq -r '.transaction.actions[0].data.code' | xxd -r -p | sha256sum
```

3. Confirm steps 1 and 2 results match

#### `diff` of system contracts
```
# diff <previous eos version>/contracts/eosio.system> <current eos version>/contracts/eosio.system
diff ~/git/mainnet-1.0.6/eos/contracts/eosio.system/ ~/git/mainnet/eos/contracts/eosio.system/

diff /home/deck/git/mainnet-1.0.6/eos/contracts/eosio.system/delegate_bandwidth.cpp /home/deck/git/mainnet/eos/contracts/eosio.system/delegate_bandwidth.cpp
157,159c157,160
<     *  While buying ram uses the current market price according to the bancor-algorithm, selling ram only
<     *  refunds the purchase price to the account. In this way there is no profit to be made through buying
<     *  and selling ram.
---
>     *  The system contract now buys and sells RAM allocations at prevailing market prices.
>     *  This may result in traders buying RAM today in anticipation of potential shortages
>     *  tomorrow. Overall this will result in the market balancing the supply and demand
>     *  for RAM over time.
176a178,179
>       eosio_assert( tokens_out.amount > 1, "token amount received from selling ram is too low" );
>
190c193,196
<       auto fee = tokens_out.amount / 200;
---
>
>       auto fee = ( tokens_out.amount + 199 ) / 200; /// .5% fee (round up)
>       // since tokens_out.amount was asserted to be at least 2 earlier, fee.amount < tokens_out.amount
>
209a216,218
>       eosio_assert( std::abs( (stake_net_delta + stake_cpu_delta).amount )
>                      >= std::max( std::abs( stake_net_delta.amount ), std::abs( stake_cpu_delta.amount ) ),
>                     "net and cpu deltas cannot be opposite signs" );
276,277c285,294
<          if ( req != refunds_tbl.end() ) { //need to update refund
<             refunds_tbl.modify( req, 0, [&]( refund_request& r ) {
---
>
>
>          // net and cpu are same sign by assertions in delegatebw and undelegatebw
>          // redundant assertion also at start of changebw to protect against misuse of changebw
>          bool is_undelegating = (net_balance.amount + cpu_balance.amount ) < 0;
>          bool is_delegating_to_self = (!transfer && from == receiver);
>
>          if( is_delegating_to_self || is_undelegating ) {
>             if ( req != refunds_tbl.end() ) { //need to update refund
>                refunds_tbl.modify( req, 0, [&]( refund_request& r ) {
# truncated output
```


#### Build abi_from_hex

Create the file `abi_from_hex.cpp`
```
/**
 *  @file
 *  @copyright defined in eos/LICENSE.txt
 */

#include <eosio/chain/asset.hpp>
#include <eosio/chain/block_log.hpp>
#include <eosio/chain/transaction.hpp>
#include <eosio/chain/block_header_state.hpp>
#include <eosio/chain/abi_serializer.hpp>
#include <eosio/chain/authority.hpp>
#include <fc/io/raw.hpp>
#include <fc/io/json.hpp>
#include <fc/crypto/hex.hpp>
#include <fc/optional.hpp>
#include <fc/reflect/reflect.hpp>
#include <fc/filesystem.hpp>

using namespace eosio;
using namespace eosio::chain;
using namespace chainbase;
using namespace fc;
using namespace std;

#include <boost/program_options.hpp>

namespace po = boost::program_options;

int main(int argc, const char **argv) {

   try {

   std::istream *in;
   std::ifstream ifn;

   if ( argc == 1 ) {
      in=&cin;
   } else {
      ifn.open(argv[1]);
      in=&ifn;
   }

   std::stringstream buffer;
   buffer << in->rdbuf();

   auto str_hex_abi = buffer.str();
   bytes bin_abi(str_hex_abi.size()/2);
   
   from_hex(buffer.str(), bin_abi.data(), bin_abi.size());
   auto abi = fc::raw::unpack<abi_def>(bin_abi.data(), bin_abi.size());
   
   cout << fc::json::to_string(abi) << std::endl;

   return 0;

   } FC_CAPTURE_AND_LOG(());
   return 1;
}
```

##### Adding `abi_from_hex` patch

https://gist.github.com/elmato/4fce5bd325ca56bf037f4f906d0a67ae

1. Download patch
```
wget https://gist.githubusercontent.com/elmato/4fce5bd325ca56bf037f4f906d0a67ae/raw/ab6daf459d64d30cfde09f26120420cf722e7303/abi_from_hex.patch -O /tmp/abi_from_hex.patch
```

2. Apply patch
```
git apply < /tmp/abi_from_hex.patch
```

3. Build `abi_from_hex`
```
cd build
cmake ..
make abi_from_hex
# location of abi_from hex: tools/abi_from_hex
```

#### `diff` of eosio.system ABI

1. Extract current abi
```
cleos get code -a /tmp/current.abi && cat /tmp/current.abi | jq '' > /tmp/current.eosio.system.json
```

2. Extract proposed ABI
```
# cleos multisig review <proposer> <proposal_name> | jq -r '.transaction.actions[1].data.abi | abi_from_hex | jq '' > /tmp/eosio-1.0.7-abi.json
cleos multisig review eoscanadacom onezeroseven | jq -r '.transaction.actions[1].data.abi | abi_from_hex | jq '' > /tmp/eosio-1.0.7-abi.json
```

3. `diff` two abi
```
diff /tmp/current.eosio.system.json /tmp/eosio-1.0.7-abi.json
```

### BP Voting on Chain

#### Create Vote

https://github.com/eoscanada/eosio.forum

(see [referendum json proposal](https://github.com/eoscanada/eosio.forum#referendum-structure-proposals) for details)

### BP Meeting Rollcall

```
cleos push action eosforumtest post '{"poster":"YOURACCOUNT", "post_uuid":"<DATE>-bp_meeting", "content":"present", "reply_to_poster":"", "reply_to_post_uuid":"", "certify":false, "json_metadata":"" }' -p YOURACCOUNT@active
```

DATE should be YYYYMMDD format.
