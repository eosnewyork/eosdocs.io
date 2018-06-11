---
title: "Frequently asked questions"
date: 2018-06-11T09:07:37-04:00
pre: "<b>3. </b>"
draft: false
weight: 4
---

#### Create Account
```
# replace <values> with your own values
# add keys to wallet
cleos --wallet-url <wallet url> --url <api endpoint url> wallet import <priv key>

# create account
# the staked amount should look like "1.0025 EOS" and require 4 numbers after the decimal
cleos --wallet-url <wallet url> --url <api endpoint url> system newaccount <creator account> <created account> <owner public key> <active public key> --stake-net "N.NNNN EOS" --stake-cpu "N.NNNN EOS" --buy-ram-kbytes 8 --transfer
```

#### Set Active Key

```
# replace <values> with your own values
cleos --wallet-url <wallet url> --url <api endpoint url> set account permission <account name> active <public key> owner
```

#### Set Owner Key

```
# replace <values> with your own values
cleos --wallet-url <wallet url> --url <api endpoint url> set account permission <account name> owner <public key> -p <account name>@owner
```

#### Vote for Producers

```
# replace <values> with your own values
# vote for multiple producers
cleos --wallet-url <wallet url> --url <api endpoint url> system voteproducer prods <account name> <producer 1> <producer 2>

# append vote for producer
cleos --wallet-url <wallet url> --url <api endpoint url> system voteproducer approve <account name> <producer>
```