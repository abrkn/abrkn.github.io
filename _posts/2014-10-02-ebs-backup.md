---
layout: post
title: Backing up an Amazon EBS volume with CoreOS
permalink: /posts/backing-up-an-amazon-ebs-volume-with-coreos
---

[CoreOS](https://coreos.com/) is all the rage these days and I find myself having to reinvent a lot of patterns. At [Justcoin](https://justcoin.com/) we produce up to 7,500 new Bitcoin addresses for our customers every day. The [key pool](https://en.bitcoin.it/wiki/Key_pool) functionality of [bitcoind](https://github.com/bitcoin/bitcoin) (Bitcoin Core) keeps 10,000 unused addresses for future use. When you ask bitcoind to create a new address it actually comes out of this pool. We’re moving over to [HD wallets](https://github.com/bitcoin/bips/blob/master/bip-0032.mediawiki) for our customers soon which could be the subject of another post.

There are three steps to the backup routine:

*   Create a scheduled task/cron job for the daily backup
*   Produce a backup file of the bitcoin wallet
*   Instruct Amazon EC2 to create a snapshot

The timer
---------

[Timers in systemd](https://www.freedesktop.org/software/systemd/man/systemd.timer.html) are unit files with the .timer file extension. A service with the same unit name but with the .service extension is run when the timer elapses. We’ll create a timer called bitcoind-backup.timer:

{% gist 597010ddb88a3f81e62b %}

I’m not sure if it’s strictly necessary to run the timer on the same CoreOS instance as the timer.

The wallet backup and the EBS snapshot
--------------------------------------

The backup service issues the bitcoind RPC command [backupwallet](https://en.bitcoin.it/wiki/Original_Bitcoin_client/API_calls_list), which “Safely copies wallet.dat to destination”. An EBS snapshot is initiated using [AWS CLI](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-snapshot.html) after the backup is completed.

{% gist 6d56579c9d2d010b31a0 %}

*   Line 8: Alex Crawford’s simple [awscli Docker image](https://registry.hub.docker.com/u/abcrawf/awscli/dockerfile/) allows management of AWS resources.
*   Line 9: My [bitcoind Docker image](https://registry.hub.docker.com/u/abrkn/bitcoind/dockerfile/) lets us issue RPC commands from the bitcoind CLI
*   Line 15, 18: We run bitcoind using Docker. This link allows the backup instance (bitcoind-backup) to connect to the live hot wallet (bitcoind)
*   Line 25–26: We store our AWS key and secret in CoreOS’ etcd
*   Line 30: We also store the AWS volume id of the hot wallet’s data directory inside etcd

Getting everything up and running
---------------------------------

```
fleetctl load bitcoind-backup.service
fleetctl load bitcoind-backup.timer
fleetctl start bitcoind-backup.timer
```


Conclusion
----------

There’s a lot of extra typing involved to accomplish tasks using CoreOS and Docker. I’d still take it over Chef any day.

Looking for a job in dev ops where you can work with CoreOS and Docker? Send an email to [andreas@justcoin.com](mailto:andreas@justcoin.com) with a link to some of your best Docker/AWS work.

Looking to get started with digital money? Check out [Justcoin Exchange](https://justcoin.com/)
