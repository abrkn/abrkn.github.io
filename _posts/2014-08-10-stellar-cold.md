---
layout: post
title: Generating a cold wallet for Stellar
permalink: /posts/generating-a-cold-wallet-for-stellar
---

There aren't any easy-to.use tools for creating Stellar cold wallets yet, so I’ll do a walkthrough here. I’m doing this from a laptop with an installation of Ubuntu 14.04 (desktop image). The computer has never been connected to the Internet.

I have already attached a printer that fortunately required no extra drivers.

## pip

Download pip from [https://launchpadlibrarian.net/171402930/python-pip\_1.5.4-1\_all.deb](https://launchpadlibrarian.net/171402930/python-pip_1.5.4-1_all.deb) on your workstation and transfer it to the laptop using an USB stick. You’ll also need these dependencies:

*   [https://launchpad.net/ubuntu/trusty/i386/python-colorama](https://launchpad.net/ubuntu/trusty/i386/python-colorama)
*   [https://launchpadlibrarian.net/165738754/python-colorama\_0.2.5-0.1ubuntu1\_all.deb](https://launchpadlibrarian.net/165738754/python-colorama_0.2.5-0.1ubuntu1_all.deb)
*   [https://launchpadlibrarian.net/172260516/python-distlib\_0.1.8-1\_all.deb](https://launchpadlibrarian.net/172260516/python-distlib_0.1.8-1_all.deb)
*   [https://launchpadlibrarian.net/170425368/python-pkg-resources\_3.3-1ubuntu1\_all.deb](https://launchpadlibrarian.net/170425368/python-pkg-resources_3.3-1ubuntu1_all.deb)
*   [https://launchpadlibrarian.net/167353302/python-urllib3\_1.7.1-1build1\_all.deb](https://launchpadlibrarian.net/167353302/python-urllib3_1.7.1-1build1_all.deb)
*   [https://launchpadlibrarian.net/170425369/python-setuptools\_3.3-1ubuntu1\_all.deb](https://launchpadlibrarian.net/170425369/python-setuptools_3.3-1ubuntu1_all.deb)
*   [https://launchpadlibrarian.net/162225403/python-html5lib\_0.999-2\_all.deb](https://launchpadlibrarian.net/162225403/python-html5lib_0.999-2_all.deb)
*   [https://launchpadlibrarian.net/161787941/python-six\_1.5.2-1\_all.deb](https://launchpadlibrarian.net/161787941/python-six_1.5.2-1_all.deb)

## libsodium

Download libsodium from [https://download.libsodium.org/libsodium/releases/LATEST.tar.gz](https://download.libsodium.org/libsodium/releases/LATEST.tar.gz) and install like this:

$ tar xzf LATEST.tar.gz  
$ cd libsodium-0.6.1/  
$ ./configure  
$ make && make check && sudo make install  
$ sudo /sbin/ldconfig

## pysodium

Download pysodium from [https://pypi.python.org/packages/source/p/pysodium/pysodium-0.6.2.tar.gz](https://pypi.python.org/packages/source/p/pysodium/pysodium-0.6.2.tar.gz#md5=cd0fc5ad93b9855a20e812d4794300e3) and install:

## stellar\_keygen
---------------

> Fetch stlelar\_keygen.py from [https://github.com/johansten/stellar\_keygen/blob/master/stellar\_keygen.py](https://github.com/johansten/stellar_keygen/blob/master/stellar_keygen.py)

## Generate the wallet

> $ python stellar\_keygen.py

You can print keys like this:

> _$ python stellar\_keygen.py \| lpr_

## Retrieving the funds

The best way to retrieve the funds is to sign the transaction offline, but the tools to do that do not seem to be ported from Ripple yet.
