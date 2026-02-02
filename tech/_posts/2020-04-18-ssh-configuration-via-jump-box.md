---
layout: post
title: "SSH Configuration via a Jump Box"
date: 2020-04-18
categories: tech
description: "Step-by-step guide to configure SSH access through a jump box (jump server) on Mac. Learn about reverse proxies and secure server connections."
---

An enterprise system often uses the concept of a jump box (or jump server) for access. A jump server essentially acts as a reverse-proxy between the Internet and hosts on the internal networks. This is done to enable a single point of entry, that facilitates security checks, throttling, common operations across hosts etc.

When we talk about proxies, the idea is that a forward proxy essentially lets us access the wider internet from inside a protected network. Let's say you're trying to access an approved download link from inside your company's network (e.g. proxy configuration in your IDE to download plugins), you will be connecting to a forward proxy.

Now, a reverse-proxy is the opposite. It is used to insulate incoming calls from a wider network to a protected group of hosts. So if someone wants to use a service deployed on your remote host, you'd most likely be creating a reverse-proxy endpoint for them to use. The reason for this is that you will be able to let them access a service without having to disclose port or actual IP information. This can have other benefits like load balancing in addition to security.

**NOTE:** Additionally, in many occasions, connection to a port via the jump box is also limited within a VPN. Depending upon how your architecture is set up, you might have to connect to a VPN client.

## Simplified connection flow

The following steps apply to a Mac.
I haven't tried this on a Windows machine, but you can look up the steps to configure the same via puTTY.

1. In your home folder, edit the SSH config file. If the folder/file doesn't exist, create one with mkdir.

```bash
vi ~/.ssh/config
```

2. Add jump box details and configure the required server you want to connect to. In most cases, you would require to provide certificates in the form of a PEM file to connect.

```bash
Host <Jumpbox Alias>
    HostName <Provide Host Name>
    User <User>
    Port <Port number>
    IdentityFile ~/.ssh/<Identity File Name>.pem

Host <Host Alias>
    HostName <Provide Host Name>
    User <User>
    IdentityFile ~/.ssh/<Identity File Name>.pem
    ProxyCommand ssh <Jumpbox Alias> -W %h:%p
```

3. Connect to your host using the Terminal.

```bash
ssh <Host Alias>
```
