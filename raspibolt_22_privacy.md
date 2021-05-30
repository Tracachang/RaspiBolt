---
layout: default
title: Privacy
nav_order: 22
---
<!-- markdownlint-disable MD014 MD022 MD025 MD033 MD040 -->
# Privacy
{: .no_toc }

We configure Tor to run your node anonymously.

---

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

Running your own Bitcoin and Lightning node at home makes you a direct, sovereign peer on the Bitcoin network.
If not configured without privacy in mind, it also tells the world that there is someone with Bitcoin at that address.
True, it's only your IP address that is visible to others, but using services like [iplocation.net](https://www.iplocation.net){:target="_blank"}, your physical address can be determined quite accurately.

Especially with Lightning, your IP address is widely used, so we need to make sure that you keep your privacy.

---

## Tor Project

We will use Tor, a free software built by the [Tor Project](https://www.torproject.org){:target="_blank"}, that allows you to anonymize internet traffic by routing it through a network of nodes, hiding your location and usage profile.

It is called "Tor" for "The Onion Router": information is encrypted multiple times with the public keys of the nodes it passes through. Each node decrypts the layer of information that corresponds to its own private key, knowing only the last and next hop of the route, like peeling an onion, until the data reaches its destination.

---

## Installing Tor

You can install install tor from manjaro repo
  ```sh
  $ sudo pacman -S tor
  ```

* Check the version of Tor (it should be 0.3.3.6 or newer) and that the service is up and running.

  ```sh
  $ tor --version
  > Tor version 0.4.5.8
  ```
  We want tor to start automatically when raspibolt boots so we will run:
  ```sh
  $ sudo systemctl enable tor
  ```

* Modify the Tor configuration by uncommenting (removing the #) or adding the following lines.

  ```sh
  $ sudo nano /etc/tor/torrc
  ```

  ```conf
  CookieAuthentication 1
  CookieAuthFile /var/lib/tor/control_auth_cookie
  CookieAuthFileGroupReadable 1
  DataDirectoryGroupReadable 1
  ```
* Make sure that user "bitcoin" belongs to the "tor" group.
  ```sh
  usermod -a -G tor bitcoin
  ```
  ... and as user, reload group settings 
  
  ```sh
  $ newgrp tor
  ```
  
* Restart Tor to activate modifications

  ```sh
  $ sudo systemctl restart tor
  ```
  Now user should have access to your Tor cookie file. 
  ```sh
  $ stat -c%a /var/lib/tor /var/lib/tor/control_auth_cookie
  ```
  should print 750 and 640. 

<script id="asciicast-xeGJH0YDOVZV719yn5rDL9GuP" src="https://asciinema.org/a/xeGJH0YDOVZV719yn5rDL9GuP.js" async></script>

Not all network traffic is routed over the Tor network.
But we now have the base to configure sensitive applications to use it.

---

Next: [Bitcoin >>](raspibolt_30_bitcoin.md)
