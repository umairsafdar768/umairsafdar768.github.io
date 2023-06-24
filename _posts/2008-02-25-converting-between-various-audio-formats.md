---
title: Strongswan PSK Based Tunnel Using Debian VMs
date: 2023-06-23 01:00:00
categories: [vpn, configuration]
tags: [strongswan, vpn, ubuntu, debian, aes, virtual machine]
math: true
---


# The Scenario

Today we will be implementing a secure communication tunnel between two Debian gateways hosted on VMware Virtual Machines. Here is a rough sketch of the network interconnect

![Network Interconnect](/assets/img/posts/post1/pic1.png){: width="700"}

We shall setup two VMware machines with Debian 12 Linux, and install *Strongswan* from source in both of them. Also we'll add two Network Interface Cards per VM so that we have two interfaces, one for the tunnel, and the other one for the subnet. After the above setup, we move towards *Strongswan* installaltion

# Setting Up Strongswan

There are two methods of setting up Strongswan in Debian 12. We can either install it using the *aptitude package manager* (apt) or we can download the source code and compile it from there.

## Installation From Package Manager

We proceed by first updating our repositories;


```bash
sudo apt update && sudo apt upgrade -y
```

Next, we use;

```bash
sudo apt install strongswan -y
```
All respective configuration files and folders can be found in the /etc directory after successfull installation.


## Installation From Source
Before going into source code compilation, we need to take care of some dependencies.

### Installing Dependencies
The Following libraries need to be installed as dependencies for strongswan.
* GMP Library
* OpenSSL Library 

The GMP library is used for mathematical operations in Strongswan and OpenSSL contains implementations of various Cryptographic Algorithms.

```bash
sudo apt install libgmp10
sudo apt install libgmp-dev
sudo apt install libssl-dev
```

### Source Compilation
We can now move towards downloading the Strongswn source. Then we will compile the source code to get a binary executable, using system C compiler and 'make' tool. Get the latest strongswan source using;  
'cd' into your /home directory, create a new directory named 'strongswan' and 'cd' into it. Then run the following command;

```bash
wget "https://download.strongswan.org/strongswan-x.x.x.tar.bz2"
```
where the letters *x.x.x* can be replaced with the latest version available. At the time of this writing, we go with version '5.9.11'  
The above command will download tar file of strongswan in the current directory. We can then use
```bash
tar xjf strongswan-x.x.x.tar.bz2
cd strongswan-x.x.x
```
to unpack the tarball and change the directory into the unpacked source of strongswan.  
Now we run the conifgure script of Strongswan. Here we can choose different functionalities including plugins that we want to include or exclude from our build. We can also use the '--help' option to see details of various configurable options. 
In our case, we'll only include the 'openssl' plugin as follows;
```bash
./configure --enable-openssl
```