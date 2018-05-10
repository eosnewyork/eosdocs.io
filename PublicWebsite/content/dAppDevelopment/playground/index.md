---
title: "Playground"
date: 2018-04-30T15:53:46-04:00
draft: false
weight: 15
---

{{< EOS_Create_Account >}}


{{%expand "How can you do the same thing from the commandline?" %}}
#### Account creation

Creating account is covered in detail [here](../accounts). But in summary all this page is doing is:

* 1. Generating a key pair to be used for the owner. 
* 2. Generating a key pair to be used for the active.
* 3. Creating the account using the public keys from each of the above. 

```
$ ./cleos create key
Private key: AAAAAAAAAAAA
Public key: CCCCCCCCCC


$./cleos create key
Private key: CCCCCCCCC
Public key: DDDDDDDDDDDD

$./cleos -H {Server} -p {Port} create account eosio mynewaccount CCCCCCCCCC DDDDDDDDDDDD
```

{{% /expand%}}

{{%expand "How can you add a contract to your new account?" %}}

#### Web based contract compiler

Creating contracts is covered in detail [here](../smartcontractbasics). But if you want to test a contract without installing all the compilers and tools, you can use a web based contract editor: 


Once you've created an account using the tool above. You can then use <a href="https://tbfleming.github.io/cib/eos.html" target="_blank">THIS</a><br>tool to compile a contract and uplod it to your new account. 

Here are some contract examples:<br>
Example 1: <a href="https://tbfleming.github.io/cib/eos.html#gist=8b2b9a60cf51afcfaeb80e88c74d66ae" target="_blank">Click Here</a><br> 
{{% /expand%}}

<hr>

{{< eosserver >}}