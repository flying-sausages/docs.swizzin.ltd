---
id: wireguard
title: Wireguard
sidebar_label: Wireguard
---

WireGuard is an extremely simple yet fast and modern VPN that utilizes state-of-the-art cryptography

## Initial Setup

Installing wireguard is easy. Simply issue the following command from SSH:

```bash main
sudo box install wireguard
```

At the end of the installation, the location of the config file for your user will be printed (`/home/<user>/.wireguard/<user>.conf`)

## How to Access

### Client Install
In order to use the Wireguard tunnel, you'll need to install the client on your local computer or mobile phone. In order to get started, please check the [Wireguard site](https://www.wireguard.com/install/) for help on installing Wireguard on the operating system of your choice.

::: note
If you prefer, an alternate client called [TunSafe](https://tunsafe.com/download) exists and is already a bit more mature than the official Wireguard client for Windows. **While the client itself is open-source and developed by a community member with prior credibility, it bears mentioning that using this client completely, 100% at your own risk as it is not developed or maintained by the Wireguard team. You have been warned.**
:::

### Client Setup

Wireguard is available on many platforms. Setting it up for use with your swizzin configuration should be fairly straight-forward, but in case you need a little help getting your client setup, here are some instructions for popular operating systems.

<!--DOCUSAURUS_CODE_TABS-->
<!--Linux / OS X-->
::: panel
1. Simply copy-paste the content of the file outputted at the end of the server setup into a file in /etc/wireguard.
```bash
sudo nano /etc/wireguard/wg0.conf
sudo chmod 600 /etc/wireguard/wg0.conf
sudo wg-quick up wg0
```
2. Wireguard should now be up and tunnelling all you traffic through swizzin.
3. On Linux systems, you can configure a systemd service to automatically run and enable this configuration on each boot:
```bash
sudo systemctl enable wg-quick@wg0
```
:::
<!--Windows-->
::: panel
1. Copy-paste the contents of the file outputted at the end of configuration into a file onto your local desktop,eg: `swizzin.conf.d`

2. Open TunSafe, click and drag the configuration file you just created to the TunSafe window. TunSafe will automatically import the client configuration and connect. Easy!
:::
<!--Android-->
::: panel
Configuration is easiest if you use utilize the QR Encode function.

1. Connect to your server from a computer and issue the commands:
```bash
u=$(whoami)
qrencode -t ansiutf8 < ~/.wireguard/$u.conf
```
2. In your client on your phone, add a new tunnel and chose the `QR Code` option. Scan the QR code which was generated in your terminal.
3. Enable the tunnel by ticking the switch.
:::
<!--iOS-->
::: panel
Configuration is easiest if you use utilize the QR Encode function.

1. Connect to your server from a computer and issue the commands:
```bash
u=$(whoami)
qrencode -t ansiutf8 < ~/.wireguard/$u.conf
```
2. In your client on your phone, add a new tunnel and chose the `QR Code` option. Scan the QR code which was generated in your terminal.
3. Enable the tunnel by ticking the switch.
:::
<!--END_DOCUSAURUS_CODE_TABS-->

::: tip Check if it worked
After configuring your Wireguard Client, [check your IP Address](https://duckduckgo.com/?q=ip+address&ia=answer).
:::

## Service Management

Service management for wireguard is a bit different from other services. Wireguard interfaces are managed by the systemd service `wg-quick@.service`.

The default location for the wg-quick service is:

```bash
/lib/systemd/system/wg-quick@.service
```

Instead of usernames, wg-quick uses the id of the user as an identifier. You can easily find the id of your user with the command: `id -u <username>`

The basic construction of the service name is:

```bash
wg-quick@wg$(id -u <username>)
```

This service corresponds to the configuration file `wg$(id -u <username>).conf` in `/etc/wireguard`

Once you have the id of your user in question (for example, 1000), you can manipulate the service by calling it with:

```
wg-quick@wg1000
```

Calling `wg1000` essentially means use the conf file `/etc/wireguard/wg1000.conf` when calling `wg-quick`.


<!--DOCUSAURUS_CODE_TABS-->
<!--Start-->
```bash
sudo systemctl start wg-quick@wg1000
```
<!--Stop-->
```bash
sudo systemctl stop wg-quick@wg1000
```
<!--Restart-->
```bash
sudo systemctl restart wg-quick@wg1000
```
<!--Enable-->
```bash
sudo systemctl enable wg-quick@wg1000
```
<!--Disable-->
```bash
sudo systemctl disable wg-quick@wg1000
```
<!--END_DOCUSAURUS_CODE_TABS-->

## Troubleshooting

### WG doesn't work for any user except for the master
The multi-user functionality has been patched in at a later stage. Please make sure to run `box update` and then remove and install wireguard again (`box remove wireguard && box install wireguard`). We have opted against patching this automatically as some administrators might not want to give their users WG access without knowing first.

### My connection is not being kept alive
This can happen when you are behind an NAT. Uncomment the following line at the end of your config. 

```plaintext
PersistentKeepalive = 25
```