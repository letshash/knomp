## Mining stratum for Spacecoin, Komodo, and other komodo smartchains.

Requirements
------------
* node v10+
* libsodium
* boost
* Redis (see https://redis.io/topics/quickstart for details)

Differences between this and Z-NOMP
------------
* This is meant for mining Spacecoin, Komodo, and Komodo smartchains
* Founders, Treasury, and other ZEC/ZEN specific stuff is removed

Upgrade
-------------
Please be sure to backup your `./coins` and `./pool_configs` directory before upgrading

Kill your running pool (CTRL-C)
```shell
cd knomp
git pull
npm install
npm start
```

Install Daemon
-------------
Some initial setup
```shell
# The following packages are needed to build both Spacecoin and this stratum:
sudo apt-get update
sudo apt-get install build-essential pkg-config libc6-dev m4 g++-multilib autoconf libtool ncurses-dev unzip git python python-zmq zlib1g-dev wget libcurl4-openssl-dev bsdmainutils automake curl libboost-dev libboost-system-dev libsodium-dev jq redis-server nano -y
```
Now, let's build Spacecoin
```shell
git clone https://github.com/spaceworksco/spacecoin
cd spacecoin
zcutil/fetch-params.sh
zcutil/build.sh -j8
strip src/komodod
strip src/komodo-cli
```

Now, let's start Spacecoin
```shell
cd ~/spacecoin/src
./spacecoind
```

Install Pool
-------------
Once SPACE is synced up we can configure the stratum.

We need node and npm installed

```shell
cd ~
curl -sL https://deb.nodesource.com/setup_10.x | sudo -E bash -
```

Now, let's build our stratum and run it. This will install the pool. Then you need to configure knomp and the SPACE pool with the rpcuser/pass from the SPACE.conf file, as well as your pool/fees address.
```shell
git clone https://github.com/spaceworksco/knomp
cd knomp
npm install
cp config_example.json config.json
# then configure it
nano config.json
cp pool_configs/example/space.json pool_configs/space.json
# then configure it
nano pool_configs/space.json
```

Finally we are ready to start the pool software

```shell
npm start
```

If all went well knomp should start without error and you should be able to browse to your pool website on your server via port 8080.

## More Info:

Disable Coinbase Mode
-------------
This mode uses -pubkey to tell the daemon where the coinbase should be sent, and uses the daemons coinbase transaction rather then having the pool create the coinabse transaction. This enables special coinbase transactions, such as ac_founders and ac_script or new modes with CC vouts in the coinbase not yet created, it will work with all coins, except Full Z support described below.

To enable it, change the value in the `coins/space.json` to `"disablecb" : true`

The pool fee is taken in the payment processor using this mode, and might not be 100% accurate down to the single satoshi, so the pool address may end up with some small amount of coins over time.

Payment Processing
-------------
Please note that the default configs generated are for solo mining. If you wish to create a public pool please modify the configs like in this [example config](https://github.com/z-classic/z-nomp/blob/master/pool_configs/komodo_example.json)

There is now a config option you can add to your pool_configs/coin.json to toggle making an attempt at a payment upon pool startup.

```
"paymentProcessing": {
    "payOnStart": true,
    ...
}
```

Invalid Worker Addresses
-------------
You can add an option to your pool_config to have any miners that mine with an invalid address (if they somehow get through) to pay out to an address of your choosing
```
"invalidAddress":"zsValidAddressOfYourChoosingThatsNotThePoolZAddress"
```

Sapling and Sapling Payment Support
-------------
In coins/space.json file:
```
"privateChain": true,
"burnFees": true,
"sapling": true
```

In pool_config:
```
"zAddress": "zsPoolsSaplingAddress",
"walletInterval": 2,
"validateWorkerUsername": true,
"paymentProcessing": {
    "minConf": 5,
    "paymentInterval": 180,
    "maxBlocksPerPayment": 20,
```

More Resources
-------------
[Further info on config](https://github.com/zone117x/node-open-mining-portal#2-configuration) and some [sample configs](https://github.com/z-classic/z-nomp)

License
-------------
Forked from ComputerGenie repo (deleted)

Released under the GNU General Public License v2
http://www.gnu.org/licenses/gpl-2.0.html

_Forked from [z-classic/z-nomp](https://github.com/z-classic/z-nomp) which is incorrectly licensed under MIT License - see [zone117x/node-open-mining-portal](https://github.com/zone117x/node-open-mining-portal)_
