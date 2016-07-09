---
layout: post
title:  'Setup PPTP VPN on Ubuntu Vultr VPS'
date:   2016-06-05 12:00:00
tags:	technology
myTag:	server
headimg: vultr.png
---

### Setup PPTP VPN on Ubuntu Vultr VPS

If you are reading this artical, I'm assuming you have purchased a Vultr VPS and setup a Ubuntu Server. If you need to setup a VPN, you may probably a Chinese :) 

There is already an artical on Vultr Docs [Setup PPP VPN on Debian/Ubuntu](https://www.vultr.com/docs/setup-ppp-vpn-on-debian-ubuntu) telling people how to setup a VPN. However, that artical is a little bit old and lack command explanation (I don't like to run command that I don't know). OK, let's start.

### 1. Install Packages

Installing pptpd packages with the following code:

	apt-get install pptpd

### 2. Configuration

Configure `/etc/pptpd.conf` and `/etc/ppp/pptpd-options`.


	vim /etc/pptpd.conf

		option /etc/ppp/pptpd-options		#Specifies the location of the PPP options file.

		localip 192.168.0.1					#Your VPN local IP
		remoteip 192.168.0.100-245			#Your client IP that connect to VPN

	vim /etc/ppp/pptpd-options

		name pptpd							#Name of the local system for authentication purposes

		refuse-pap							#Refuse Password Authentication Protocal (PAP)
		refuse-chap							#Refuse Challenge Handshake Authentication Protocol (CHAP)
		refuse-mschap						#Refuse Microsoft-CHAP

		require-mschap-v2					#Use Microsoft-CAHP-Version Two
		require-mppe-128					#Use Microsoft Point-to-Point Encryption 128 bit
		proxyarp							
		nodefaultroute						
		lock
		nobsdcomp
		ms-dns 8.8.8.8						#DNS for Windows user
		ms-dns 8.8.4.4						#DNS for Windows user
		debug
		dump
		idle 300

Add username and password for each account.

	vim /etc/chap-secrets

		USERNAME	pptpd	PASSWORD	*

Restart the pptpd server.

	/etc/init.d/pptpd restart

### 3. Enable Forwarding

Enable packet forwarding for IPv4.

	vim /etc/sysctl.conf

		net.ipv4.ip_forward=1				#Uncomment this line to enable packet forwarding for IPv4

Make the configuration permanent.

	sysctl -p

### 4. Setup Routing

Create a script for iptable configuration.

	vim /etc/network/if-pre-up.d/route

		iptables -t nat -A POSTROUTING -o ens3 -j MASQUERADE
		iptables --table nat --append POSTROUTING --out-interface ppp0 -j MASQUERADE
		iptables -I INPUT -s 192.168.10.0/24 -i ppp0 -j ACCEPT
		iptables --append FORWARD --in-interface ens3 -j ACCEPT

You shold run `ifconfig` to check your port is `ens3` or `eth0`.

Setup this script to be executable.

	chmod +x /etc/network/if-pre-up.d/route

Run the script

	/etc/network/if-pre-up.d/route

### 5. Complete

	Setup your mobile phone or computer to connect to your own VPN.

	
	
### Reference

1. [Setup PPP VPN on Debian/Ubuntu](https://www.vultr.com/docs/setup-ppp-vpn-on-debian-ubuntu)
2. [CHAP versus PAP](http://www.tldp.org/LDP/nag/node120.html)
3. [Linux VPN服务器简易配置](http://blog.csdn.net/china_cctv/article/details/6994659)
