# Setting up Development Environment

## Install Node.js

Install Node.js by your favorite method, or use Node Version Manager by following directions at https://github.com/creationix/nvm

```bash
nvm install v4
```

## Fork and Download Repositories

To develop rtmcore-node:

```bash
cd ~
git clone git@github.com:<yourusername>/rtmcore-node.git
git clone git@github.com:<yourusername>/rtmcore-lib.git
```

To develop raptoreum or to compile from source:

```bash
git clone git@github.com:<yourusername>/raptoreum.git
git fetch origin <branchname>:<branchname>
git checkout <branchname>
```
**Note**: See raptoreum documentation for building raptoreum on your platform.


## Install Development Dependencies

For Ubuntu:
```bash
sudo apt-get install libzmq3-dev
sudo apt-get install build-essential
```
**Note**: Make sure that libzmq-dev is not installed, it should be removed when installing libzmq3-dev.


For Mac OS X:
```bash
brew install zeromq
```

## Install and Symlink

```bash
cd rtmcore-lib
npm install
cd ../rtmcore-node
npm install
```
**Note**: If you get a message about not being able to download raptoreum distribution, you'll need to compile raptoreumd from source, and setup your configuration to use that version.


We now will setup symlinks in `rtmcore-node` *(repeat this for any other modules you're planning on developing)*:
```bash
cd node_modules
rm -rf rtmcore-lib
ln -s ~/rtmcore-lib
rm -rf raptoreumd-rpc
ln -s ~/raptoreumd-rpc
```

And if you're compiling or developing raptoreum:
```bash
cd ../bin
ln -sf ~/raptoreum/src/raptoreumd
```

## Run Tests

If you do not already have mocha installed:
```bash
npm install mocha -g
```

To run all test suites:
```bash
cd rtmcore-node
npm run regtest
npm run test
```

To run a specific unit test in watch mode:
```bash
mocha -w -R spec test/services/raptoreumd.unit.js
```

To run a specific regtest:
```bash
mocha -R spec regtest/raptoreumd.js
```

## Running a Development Node

To test running the node, you can setup a configuration that will specify development versions of all of the services:

```bash
cd ~
mkdir devnode
cd devnode
mkdir node_modules
touch rtmcore-node.json
touch package.json
```

Edit `rtmcore-node.json` with something similar to:
```json
{
  "network": "livenet",
  "port": 3001,
  "services": [
    "raptoreumd",
    "web",
    "insight-api",
    "insight-ui",
    "<additional_service>"
  ],
  "servicesConfig": {
    "raptoreumd": {
      "spawn": {
        "datadir": "/home/<youruser>/.raptoreumd",
        "exec": "/home/<youruser>/raptoreum/src/raptoreumd"
      }
    }
  }
}
```

**Note**: To install services [insight-api](https://github.com/Raptor3um/insight-api) and [insight-ui](https://github.com/Raptor3um/insight-ui) you'll need to clone the repositories locally.

Setup symlinks for all of the services and dependencies:

```bash
cd node_modules
ln -s ~/rtmcore-lib
ln -s ~/rtmcore-node
ln -s ~/insight-api
ln -s ~/insight-ui
```

Make sure that the `<datadir>/raptor.conf` has the necessary settings, for example:
```
server=1
whitelist=127.0.0.1
txindex=1
addressindex=1
timestampindex=1
spentindex=1
zmqpubrawtx=tcp://127.0.0.1:28332
zmqpubhashblock=tcp://127.0.0.1:28332
rpcallowip=127.0.0.1
rpcuser=raptoreum
rpcpassword=local321
```

From within the `devnode` directory with the configuration file, start the node:
```bash
../rtmcore-node/bin/rtmcore-node start
```
