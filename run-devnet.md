# Run State Expiry DevNet

The current State Expiry solution based on BEP206 and BEP215 has been implemented and open-sourced. You can try to run a State Expiry Devnet based on the implemented code.

## Repo

bsc: [https://github.com/node-real/bsc/tree/state\_expiry\_develop](https://github.com/node-real/bsc/tree/state_expiry_develop)

bsc-deploy: [https://github.com/bnb-chain/bsc-deploy/tree/state-expiry-dev](https://github.com/bnb-chain/bsc-deploy/tree/state-expiry-dev)

New Repo: [https://github.com/node-real/state-expiry-poc/](https://github.com/node-real/state-expiry-poc/)

## Usage

```plain
# download bsc-deploy
git clone https://github.com/bnb-chain/bsc-deploy --branch state-expiry-dev bsc-deploy-state-expiry
cd bsc-deploy-state-expiry
git submodule update --init --recursive

# download bsc
git clone https://github.com/node-real/bsc --branch state_expiry_develop bsc-state-expiry
# compile & copy to bsc-deploy-state-expiry/bin
cd bsc-state-expiry
make geth
go build -o bootnode ./cmd/bootnode
cp build/bin/geth ../bsc-deploy-state-expiry/bin
cp bootnode ../bsc-deploy-state-expiry/bin
```

### Deploying 3 archive nodes

```plain
cd bsc-deploy-state-expiry
# Deploying 3 archive nodes
# modifies the genesis such that the first account of scripts/asset/test_account.json is the initBnbHolder
bash scripts/clusterup_set_first.sh start
```

### Deploying 2 full node + 1 archive node as default

```plain
# Deploying 2 full node + 1 archive node as default
bash scripts/clusterup_pruning_test.sh start
```

### Stop cluster nodes

```plain
# Stop cluster nodes
bash scripts/clusterup_set_first.sh stop
```

### Restart cluster nodes

```plain
# restart cluster nodes, do not delete previous data, restart 3 nodes as default
bash scripts/clusterup_set_first.sh restart
```

### Testing BNB transfer

```plain
cd bsc-deploy-state-expiry
cd test-script
# The script keeps sending transactions until ctrl-c
go run test_bnb_transfer.go
```

### Deploying BEP20 Token Contract

```plain
cd bsc-deploy-state-expiry
cd test-contract/deploy-BEP20/
# install deps
npm install
# deploy BEP20 Token
npx hardhat run scripts/deploy.js
```

### Transfer BEP20 Token & Read Balance

```plain
cd test-contract/deploy-BEP20/
# This will transfer BEP20 Token from scripts/asset/test_account.json first account
# This script only transfer once
npx hardhat run scripts/transfer.js
# read sender & receiver's balance
npx hardhat run scripts/read-balance.js
```

you should wait BEP20 transfer state expired, the default epoch period in testing env is 50 blocks. If you transfer token at Epoch0, you should wait until Epoch2.

  

when expired and run `npx hardhat run scripts/read-balance.js`, you may got `ProviderError: Access expired state` .

### Revive contract states

when state is expired, you should run this script to revive the states used in above transfer.

```plain
cd test-script
# The script will revive sender & receiver state, and exe a new transfer token
go run test_bep20_witness_revive.go
```

### Useful RPC Query

```plain
# query best height
curl -H "Content-Type: application/json" -X POST --data '{"jsonrpc":"2.0","method":"eth_blockNumber","params":[],"id":83}' 127.0.0.1:8502

# query block
curl -H "Content-Type: application/json" -X POST --data '{"jsonrpc":"2.0","method":"eth_getBlockByNumber","params":["0x3", true],"id":83}' 127.0.0.1:8502

# query tx
curl -H "Content-Type: application/json" -X POST --data '{"jsonrpc":"2.0","method":"eth_getTransactionByHash","params":["0x12beecfb1adb7d874c4714a7871e23cf70baef612235d1276568611460927f18"],"id":83}' 127.0.0.1:8502

# query tx receipt
curl -H "Content-Type: application/json" -X POST --data '{"jsonrpc":"2.0","method":"eth_getTransactionReceipt","params":["0x782192568c8ee3393e3f3e9b7ac46e231d3cbe0b96941b642e28220ba343209b"],"id":83}' 127.0.0.1:8502
```

  

## Config

The default genesis config is below, default EpochPeriod is 200, the state will expired in next next epoch, so it will expired in max 20 minutes.

```json
{
  "config": {
    "chainId": 714,
    "homesteadBlock": 0,
    "eip150Block": 0,
    "eip150Hash": "0x0000000000000000000000000000000000000000000000000000000000000000",
    "eip155Block": 0,
    "eip158Block": 0,
    "byzantiumBlock": 0,
    "constantinopleBlock": 0,
    "petersburgBlock": 0,
    "istanbulBlock": 0,
    "muirGlacierBlock": 0,
    "ramanujanBlock": 0,
    "nielsBlock": 0,
    "mirrorSyncBlock":1,
    "brunoBlock": 1,
    "eulerBlock": 2,
    "gibbsBlock": 3,
    "moranBlock": 4,
    "planckBlock": 5,
    "lubanBlock": 6,
    "platoBlock": 7,
    "claudeBlock": 10,
    "elwoodBlock": 20,
    "parlia": {
      "period": 3,
      "epoch": 200,
      "stateEpochPeriod": 10
    }
  }
}
```

`claudeBlock` is the state expiry first hard fork, `elwoodBlock` is the second hard fork, parlia.epoch is the EpochPeriod.