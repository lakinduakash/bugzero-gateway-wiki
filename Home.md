# Welcome to the Bugzero Gateway wiki!

Bug Zero Gateway is a separate module that provides an interface for controlling access to the bounty program for hackers. So the organization which hosts the bounty program can control access to their program.

This project basically provides a REST API to control access. So anyone can use this to build their own interface.

This repository contains automated VPN service and REST API.

The API provides the following functionalities.

* Add new users
* Update existing users
* Revoke access to the VPN service
* List users that currently registered
* Obtain logs with filtering

## Setting things up

This is consists of two parts.
1. setting up VPN gateway
2. setting up API server

### Setting up the VPN server

**Requirements**
* Ubuntu 18.04 server

Clone the repository and type `cd scripts`.

First, you should run `init-gateway.sh` script as the root user. (Ex: `sudo ./init-gateway.sh`) Then it will prompt some inputs to fill. 
After that, a complete VPN server is ready.

It will ask the following configurations when it initializes.

```
--- Configuration: VPN settings ---

Network interface: ens4
External IP: 35.238.155.30

Hostname for VPN (default: 35.238.155.30.sslip.io): 
VPN username: admin
VPN password (no quotes, please): 
Confirm VPN password:
```

The default DNS to the VPN server use the sslip.io service. You can put your own domain name if it points to the correct IP address. Then you have to add one VPN user to the server.

```
--- Configuration: general server settings ---

Timezone (default: Europe/London): 
Email address for sysadmin (e.g. j.bloggs@example.com): admin@bugzero.io
Desired SSH log-in port (default: 22):
```

Then it will be asked to configure Timezone and admin email. Default values are shown in brackets.

That's it. 

The script will generate CA for you. You need to obtain the certificate signed by that CA. 
Then the client can connect using that certificate.

If you want to put your own CA and certificates do the following before executing the script.

#### Preparing installation for custom certificates

You need 3 certificates files.
* CA file (chain file) `chain.pem`
* A certificate which is signed by above CA `cert.pem`
* Corresponding Private key file `private.key`

After preparing those files you can run `./init-gateway.sh` script as follows
`sudo ./init-gateway.sh --disable-cert-gen  --private-key private.key --ca chain.pem` --cert cert.pem`

Then you can proceed with other steps as usual.

Copy and keep `CA-cert.pem` `cert.pem` files from `/etc/vpncert/${vpn_host_address}` 
since you need to send those files to client to configure VPN connection to server

After successful execution, a VPN server is up and client configuration will be written to the current directory. Following files will e available.

- `vpn-instructions.txt`
- `vpn-ios-or-mac.mobileconfig`
- `vpn-ubuntu-client.sh`





 