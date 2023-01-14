---
title: How to set up WireGuard on your own Ubuntu 20.04 server
date: 2022-04-15
cover:
  image: 'img/setup-wireguard-vpn/cover.avif'
tags: ['VPN', 'Wireguard']
---

## Introduction

[WireGuard](https://www.wireguard.com/) is a lightweight communication protocol that implements encrypted virtual privacy network (VPN).

**In my humble opinion, if you are simple user, you should use VPN, because:**

1. It gives you more freedom to access the internet safely from your smartphone or laptop when connected to a doubt network, like any public WiFi.
2. You have an access to websites blocked in your country for some reasons.

<!-- **Here are just a few reasons why WireGuard is so awesome:** -->

## WireGuard is so awesome. But why?

### Minimal code base

In fact, it's implemented in less then 4,000 lines of code.
It's much **easier to audit and reviewing the code** for security vulnerabilities.

For example, OpenVPN has approximently 600,000 and IPsec around 400,000.
WireGuard stands out significantly.

### Modern cryptography

- [Noise protocol framework](http://www.noiseprotocol.org/)
- [Curve25519](https://cr.yp.to/ecdh.html)
- [ChaCha20](https://cr.yp.to/chacha.html)
- [SipHash-2-4](https://www.aumasson.jp/)
- [BLAKE2s](https://www.blake2.net/)
- [HKDF](https://eprint.iacr.org/2010/264)

You can read more about WireGuard's cryptography on the [official website]() or in the [technical white paper [PDF]](https://www.wireguard.com/papers/wireguard.pdf)

### Incredibly fast

Check out the performance comparison charts done by the Jason Donenfeld, the WireGuard author.

- **Benchmarked** alongside IPSec in two modes and OpenVPN.
- **CPUs**: Intel Core i7-3820QM and Intel Core i7-5200U.
- **Ethernet Cards**: Intel 82579LM and Intel I218LM gigabit Ethernet.

![WireGuard performance charts published by author.](img/setup-wireguard-vpn/performance-by-author.png)

If you're interested in setting up WireGuard on your own server, keep reading.

## Prerequisites

To follow this tutorial, you will need:

- The setup steps are very similar, so you're free to use any Linux distribution you want, but in this guide I will use Ubuntu 20.04.
  I would recommend you choosing a server with unlimited traffic or at least 1000 GB.
- You will need a client machine that you will use to connect to your WireGuard server. In that guide, we will create configs for two clients, **android** and **linux**.

## Setup steps

Connect to your server.

```bash
ssh root@<ip>
```

### Step 1 - Update your system

Update system packages to up-to-date version.

```bash
apt update
apt upgrade
```

I also prefer to use `neovim` as my editor.

```bash
apt install neovim
```

### Step 2 - Installing WireGuard on server

```bash
apt install wireguard
```

### Step 3 - Enable IP forwarding on the server

```bash
echo "net.ipv4.ip_forward=1" >> /etc/sysctl.conf
```

Check out that the string was added.

```bash
sysctl -p
```

```output
net.ipv4.ip_forward = 1
```

### Step 4 - Generate public and private keys of server and clients

Change directory to `/etc/wireguard`.

```bash
cd /etc/wireguard
```

Generate public and private server keys.

```bash
wg genkey | tee privatekey | wg pubkey | tee publickey
```

Then, generate keys for our clients.
Initialy, I prefer to create folder with the nickname or device of client that will use VPN.
In this guide, I will create configs for two clients, **android** and **linux**.

```bash
mkdir android
mkdir linux
```

Then, generate keys inside created folders.

```bash
wg genkey | tee android/privatekey | wg pubkey | tee android/publickey
wg genkey | tee linux/privatekey | wg pubkey | tee linux/publickey
```

### Step 5 - Create server config

Create config file and edit it.

```bash
touch wg0.conf
```

**wg0.conf** will result in an interface named **wg0** therefore you can rename the file if you fancy something different.

```bash
nvim wg0.conf
```

Copy and paste following code to the `wg0.conf` file.

```bash {linenos=true}
[Interface]
Address = 10.0.0.1/24
PostUp = iptables -A FORWARD -i %i -j ACCEPT; iptables -A FORWARD -o %i -j ACCEPT; iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE
PostDown = iptables -D FORWARD -i %i -j ACCEPT; iptables -D FORWARD -o %i -j ACCEPT; iptables -t nat -D POSTROUTING -o eth0 -j MASQUERADE
ListenPort = 51820
PrivateKey = <Server Private Key>

[Peer]
PublicKey = <Android client public key>
AllowedIPs = 10.0.0.5/32

[Peer]
PublicKey = <Linux client public key>
AllowedIPs = 10.0.0.6/32
```

Take a look at server's `PrivateKey` and peer's `PublicKey` sections.
It requires to copy and paste keys we generated recently.

### Step 5 - Start the WireGuard server

Now start the service.

```bash
systemctl enable wg-quick@wg0
systemctl start wg-quick@wg0
```

Double check that the WireGuard service is active with the following command. You have to see `active (running)` in the output:

```bash
wg show wg0
```

```output
interface: wg0
  public key: <Public server key here>
  private key: (hidden)
  listening port: 51820

peer: <Public adroid client key here>
  allowed ips: 10.0.0.5/32

peer: <Public linux client key here>
  allowed ips: 10.0.0.6/32
```

```bash
systemctl status wg-quick@wg0
```

```output
● wg-quick@wg0.service - WireGuard via wg-quick(8) for wg0
     Loaded: loaded (/lib/systemd/system/wg-quick@.service; enabled; vendor preset: enabled)
     Active: active (exited) since Thu 2022-04-14 19:08:04 UTC; 49min ago
       Docs: man:wg-quick(8)
             man:wg(8)
             https://www.wireguard.com/
             https://www.wireguard.com/quickstart/
             https://git.zx2c4.com/wireguard-tools/about/src/man/wg-quick.8
             https://git.zx2c4.com/wireguard-tools/about/src/man/wg.8
    Process: 32190 ExecStart=/usr/bin/wg-quick up wg0 (code=exited, status=0/SUCCESS)
   Main PID: 32190 (code=exited, status=0/SUCCESS)

Apr 14 19:08:04 wireguard systemd[1]: Starting WireGuard via wg-quick(8) for wg0...
Apr 14 19:08:04 wireguard wg-quick[32190]: [#] ip link add wg0 type wireguard
Apr 14 19:08:04 wireguard wg-quick[32190]: [#] wg setconf wg0 /dev/fd/63
Apr 14 19:08:04 wireguard wg-quick[32190]: [#] ip -4 address add 10.0.0.1/24 dev wg0
Apr 14 19:08:04 wireguard wg-quick[32190]: [#] ip link set mtu 1420 up dev wg0
Apr 14 19:08:04 wireguard wg-quick[32190]: [#] iptables -A FORWARD -i wg0 -j ACCEPT; iptables -A FORWARD -o wg0 -j ACCEPT; iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE
Apr 14 19:08:04 wireguard systemd[1]: Finished WireGuard via wg-quick(8) for wg0.
```

### Step 6 - Create client config (Android)

Create file called **android-client.conf** and add the following content.

```bash
[Interface]
PrivateKey = <Android client private key>
Address = 10.0.0.5/24
DNS = 1.1.1.1

[Peer]
PublicKey = <Server public key>
Endpoint = <SERVER-IP>:51820
AllowedIPs = 0.0.0.0/0
PersistentKeepalive = 20
```

**AllowedIPs = 0.0.0.0/0** will allow you to route all traffic through the VPN tunnel.

Now we need to transfer this config to the smartphone. Certainly, you can somehow move this file, but it's more convenient to use a QR-code.

Install `qrencode`.

```bash
apt install qrencode
```

Use it

```bash
qrencode -t ansiutf8 < android-client.conf
```

After that we will see the QR code in terminal.
All we have to do is to scan it using WireGuard app on the mobile phone.
You can install it in [Play Market](https://play.google.com/store/apps/details?id=com.wireguard.android&hl=en_US&gl=US) or [F-Droid](https://f-droid.org/packages/com.wireguard.android)
