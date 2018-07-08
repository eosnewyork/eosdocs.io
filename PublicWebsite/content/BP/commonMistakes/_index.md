---
title: "Common Mistakes"
date: 2018-06-11T09:07:37-04:00
pre: "<b>3. </b>"
draft: false
weight: 4
---

## Insufficient chain state memory

{{% notice warning %}}
Do NOT just copy and paste chain-state-db-size-mb = 65536 into your config file, read how the value should be calculated to ensure that you are setting the correct value. The value will change over time.
{{% /notice %}}

eosio config.ini contains a parameter "chain-state-db-size-mb" which should match the maximum memory advertised by the network. 

```
# Maximum size (in MB) of the chain state database (eosio::chain_plugin)
chain-state-db-size-mb = 65536
```

This value should always be set to the max memory being advertised by the network. 

As a BP you should know what the expected Memory allocation is, but always confirm by querying the top top BP endpoints for the following information to confirm:


```

$cleos -u http://api.eosnewyork.io get table eosio eosio global
{
  "rows": [{
...
      "max_ram_size": "68719476736",
...
    }
  ],
  "more": false
}

```
#### Conversion math:

Then perform the math below to convert to MB

68719476736 / 1024 / 1024 = __65536__ 

So at the time of writing, chain-state-db-size-mb should be set to __65536__