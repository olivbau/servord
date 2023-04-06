# servord

A simple ord server setup for a VPS.

## Prerequisites

* `apt update && apt upgrade -y && apt install -y git snapd screen && snap install bitcoin-core`
* `git clone https://github.com/olivbau/servord && cd servord`
* Install caddy: https://caddyserver.com/docs/install
* Install ord: https://github.com/casey/ord#installation

## Setup UFW
```bash
ufw allow ssh && allow http && allow https
ufw deny 8080 && ufw deny 8332 && ufw deny 28332 && ufw deny 28333
ufw enable

ufw status
```

## Run bitcoin daemon

```bash
# Start a screen session
screen -S servord-bitcoind

# Run bitcoin daemon
bitcoin-core.daemon -chain=main -server -txindex -prune=0 -port=8333 -rpcport=8332

# Detach from a screen session
# ctrl + a + d
```

Location of bitcoin data dir and cookie file are:
* ~/snap/bitcoin-core/common/.bitcoin
* ~/snap/bitcoin-core/common/.bitcoin/.cookie


## Run ord server

```bash
# Start a screen session
screen -S servord-server

# Run ord server
/root/bin/ord --index-sats --bitcoin-data-dir ~/snap/bitcoin-core/common/.bitcoin server --address 127.0.0.1 --http-port 8080

# Detach from a screen session
# ctrl + a + d
```

## Run caddy

```bash
# Start a screen session
screen -S servord-caddy

# Set environment variables
export ORD_HOST="mydomain.com"
export ORD_BASIC_AUTH="my_username $(caddy hash-password --plaintext 'my_password')"

# Run caddy server
caddy run --config ./Caddyfile

# Detach from a screen session
# ctrl + a + d
```

## Useful commands

```bash
# List screen sessions
screen -ls

# Attach to a screen session
screen -r servord-server
screen -r servord-bitcoind
screen -r servord-caddy

# Detach from a screen session
# ctrl + a + d
```