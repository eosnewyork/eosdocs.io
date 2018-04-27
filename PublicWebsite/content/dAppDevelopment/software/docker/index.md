---
title: "Docker"
date: 2018-04-26T16:12:15-04:00
weight: 1
draft: false
---

#### Docker 

```
$ git clone https://github.com/EOSIO/eos.git --recursive
$ cd eos/Docker
$ docker build . -t eosio/eos
```

Once you have the docker image available, run the following to get and interactive shell that will allow you to execute any of the commands. 

```
$ docker run --rm -it eosio/eos bash
```
