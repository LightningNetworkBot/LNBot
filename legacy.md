# LNBot

## Installing bitcoind

```
sudo apt-add-repository ppa:bitcoin/bitcoin
sudo apt-get update
sudo apt-get install bitcoind
```

Create `bitcoin.conf`:\
`mkdir ~/.bitcoin` \
`touch ~/.bitcoin/bitcoin.conf`

Copy and paste below to `bitcoin.conf` that was just created
```
server=1
testnet=1
daemon=1
zmqpubrawblock=tcp://127.0.0.1:28332
zmqpubrawtx=tcp://127.0.0.1:28333
rpcuser=user
rpcpassword=passwd
```

## Running bitcoind
Run the following command to start `bitcoind` server:\
`bitcoind`

Check the current block height:\
`bitcoin-cli getblockcount`

If it is equal to block height shown at https://blockstream.info/testnet/, that means node is fully synced.

## Installing lnd
Download `golang`:\
`wget https://dl.google.com/go/go1.12.linux-amd64.tar.gz`

Install `golang`:\
`sudo tar -C /usr/local -xzf go1.12.linux-amd64.tar.gz`

Add the line below to `.bashrc` file and run `source ~/.bashrc`:\
`export PATH=$PATH:/usr/local/go/bin`  

### Installing lnd with sphinx send enabled
```
go get -d github.com/lightningnetwork/lnd
cd ~/go/src/github.com/lightningnetwork/lnd
git checkout v0.6.1-beta
git fetch origin pull/2455/head:sphinx-send
git checkout sphinx-send
make && make install
```

Add this line to `.bashrc` file and run `source ~/.bashrc`:\
`export PATH=$PATH:~/go/bin`

## Installing Tor
`sudo apt install apt-transport-https`

Add the following 2 entries to `/etc/apt/sources.list`:
```
deb https://deb.torproject.org/torproject.org xenial main
deb-src https://deb.torproject.org/torproject.org xenial main
```

Run the following commands:
```
curl https://deb.torproject.org/torproject.org/A3C4F0F979CAA22CDBA8F512EE8CBC9E886DDD89.asc | sudo gpg --import 
sudo gpg --export A3C4F0F979CAA22CDBA8F512EE8CBC9E886DDD89 | sudo apt-key add -
sudo apt update
sudo apt install tor deb.torproject.org-keyring
```

Add the following lines to /etc/tor/torrc:
```
SOCKSPort 9050
Log notice stdout
ControlPort 9051
CookieAuthentication 1
```
Then run the `tor` with:\
`tor &`

## Configuring lnd
Create `lnd.conf`:\
`touch ~/.lnd/lnd.conf`\
And paste the following:
```
[Application Options]
alias=myalias
debuglevel=info
debughtlc=true
;externalip=

[Bitcoin]
bitcoin.active=1
bitcoin.testnet=1
bitcoin.node=bitcoind
bitcoin.defaultchanconfs=1

[Bitcoind]
bitcoind.rpcuser=user
bitcoind.rpcpass=passwd
bitcoind.zmqpubrawblock=tcp://127.0.0.1:28332
bitcoind.zmqpubrawtx=tcp://127.0.0.1:28333

[Tor]
tor.active=true
tor.v3=true
tor.streamisolation=true
tor.privatekeypath=~/.lnd/v3_onion_private_key
```

## Running lnd
Run `lnd` with the command:\
`lnd &`

### Creating a wallet
When `lnd` is run for the first time, a new wallet has to be created:\
`lncli create`\
This will prompt for a wallet password, and optionally a cipher seed passphrase.

After creation of the wallet, `lnd` will sync with the network which can take up to an hour depending on the machine.\
After the sync, create a new wallet address:\
`lncli newaddress np2wkh`\
And send some testnet Bitcoin to this address from https://tbtc.bitaps.com/.

### Creating Channels
To open a channel to 1ml.com's public node with a capacity of 100,000 satoshi.\
Run the following:
```
lncli connect 0217890e3aad8d35bc054f43acc00084b25229ecff0ab68debd82883ad65ee8266@23.237.77.11:9735
lncli openchannel --node_key=0217890e3aad8d35bc054f43acc00084b25229ecff0ab68debd82883ad65ee8266 --local_amt=100000
```
