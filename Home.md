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
(You can also use API to obtain these files)

After successful execution, a VPN server is up and client configuration will be written to the current directory. Following files will e available.

- `/etc/vpn-instructions.txt`
- `/etc/vpn-ios-or-mac.mobileconfig`
- `/etc/vpn-ubuntu-client.sh`

### Setting up the API server

The API server contacts the VPN service through SSH. So you have to enable the SSH daemon in the VPN server and allow API server to access.

The API server is authenticated using JWT. So, first of all, you need to generate a key pair and get the public key and keep the private key securely for token generation.

You need to have a base64 version of the public key.

Example to generate key files and convert them to base64 in Linux as follows,
```sh
openssl genrsa -out private.pem 4096
openssl rsa -in private.pem -outform PEM -pubout -out public.pem

# Get the base64 string
cat public.pem| base64
```

#### Using docker

You can clone the repository and build the docker image and then you can easily deploy.

You need to `cd` into `api` folder after cloning the repository.

Then you can build the docker image using the following.

You need to have a base64 version of the public key.

```
docker build \
            --tag "bugzero-gateway/api \
            --build-arg AUTH_PUBLIC_KEY="$BASE_64_PUB_KEY" .
```

Then you can run the built container. Before that make sure you pass the following environment variables into the container.

```sh

PORT=3000 #Port of the API server
AUTH_PUBLIC_KEY=base64encoded_public_key # This should be added in a single line. If you specify in build arg this is optional.
AUTH_PRIVATE_KEY=base64encoded_private_key # Not required for server, only for testing
LOG_LEVEL=error # Valid values are debug,info,error. Default is debug

SKIP_TOKEN_CHECK=0 # For testing purpose only. If you set it as 1 JWT token will not be checked

#Connection parameters for vpn host
SSH_HOST=localhost #SSH host address
SSH_PORT=22 #SSH port
SSH_USERNAME=testssh
SSH_PASSWORD=testssh@123
SSH_USE_PASSWORD=1 # If value is 0 you need to set the private key below
SSH_PRIVATE_KEY=base64encoded_private_key # Private key in base64. Optional if username and passowrd is provided
```
#### Without docker

You need to add the public key with BASE64 in the `.env` file.

#### Start server without docker

**Requirements**
- Node 12+ runtime

After you clone the repository you can see the file called `.env.stub` in the `api` folder. It ha all the required environment variables. You need to create `.env` file inside the `api` folder and copy those variables to it. Then you can specify the variables.

Following are the variables you need to configure,

```sh

PORT=3000 #Port of the API server
AUTH_PUBLIC_KEY=base64encoded_public_key # This should be added in a single line
AUTH_PRIVATE_KEY=base64encoded_private_key # Not required for server, only for testing
LOG_LEVEL=error # Valid values are debug,info,error. Default is debug

SKIP_TOKEN_CHECK=0 # For testing purpose only. If you set it as 1 JWT token will not be checked

#Connection parameters for vpn host
SSH_HOST=localhost #SSH host address
SSH_PORT=22 #SSH port
SSH_USERNAME=testssh
SSH_PASSWORD=testssh@123
SSH_USE_PASSWORD=1 # If value is 0 you need to set the private key below
SSH_PRIVATE_KEY=base64encoded_private_key # Private key in base64. Optional if username and passowrd is provided
```
After you set the correct environment variables, you can start the server. Before that, you need to run `npm install` inside the `api` folder.

Then start the API server by executing `npm start`

If server is successfully started you can see a message like this `[2020-08-15T02:42:01.628] [INFO] default - Server started on port 3000`





 