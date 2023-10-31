# TON Performance Test

31 October 2023, 15:00 UTC at https://live.ton.org.

## Tech Details

#### Separate testnet on 256 validator nodes

256 cloud servers (`ecs.c6.6xlarge`, `ubuntu_22_04_x64_20G_alibase_20230613.vhd`) rented in Alibaba Cloud for validator nodes.

A separate testnet has been launched.

#### Target network configuration

Only one workchain. We aim to split the network into 512 shardchains.

#### Validators software

Validator nodes work on the upcoming version of TON, which after thorough testing will be release on TON mainnet.

This update is called "Accelerator" and contains a number of improvements and optimizations. 

Source code - https://github.com/ton-blockchain/ton/tree/accelerator 

Commit hash - `3466631f620a5227fda69fb175c019fed82665c6`

During the test, binaries downloaded from GitHub Autobuild are used - https://github.com/ton-blockchain/ton/actions/runs/6575234473

Binary checksum obtained by `sha256sum` - `aeddc5c4ea7f09299590351f5575bc423f8a3bab58017b9dfa5e4e2bf4d0b731`

Autobuild builds the source code on GitHub side from the "Accelerator" branch;

#### Smart contract generating load

A special smart contract generating transactions has been developed.

Source code - https://github.com/ton-blockchain/benchmark-contracts

Commit hash - `3355860b97e22693b710ae1d2ac04496b8cf21ad`

A smart contract creates child smart contracts that send messages to each other, generating a load.

The design of a smart contract is a bit like the design of a Jetton contract.

Another feature of the smart contract is that child contracts send to the parent the number of transactions they have made, so transactions are counted on-chain.

#### TPS counter of complex transactions

The smart contract has `get_counter` and `get_txs_on_period` get-methods returning the total number of transactions and the number of transactions for a given time period.

By design of the smart contract, the transaction counter is updated only by messages from child smart contracts.

Thus, it can be argued that it cannot be changed in any other way, and also that all counted transactions are not a simple transfer of coins, but the execution of a complex smart contract.

### Smart contract address

Smart contract address generating load is `Ef8tt3__gnUhKkzoFRvRefIDUKBH5gKzASHZucRW2xxJEuqK`

In TON a smart contract address is a hash of the state_init of the contract, which includes the contract code and its initial data.

You can build a smart contract locally and make sure that the calculated address matches.

```bash
git clone https://github.com/ton-blockchain/benchmark-contracts
cd benchmark-contracts
yarn
export WALLET_VERSION="v3r2"
export WALLET_MNEMONIC="help"
yarn start generateStateInit --testnet --mnemonic EQAp1wDaN83cTs1m2Uq5F_NACh8XF6WhGMIysxC5Fx11SuJb efd0d99833118bf09f9874f177bf41f4ab537154b0e04d77a0c7b7a6775a5bd1
```

#### Metrics and Dashboard

To display metrics of performance test for the user, the following infrastructure is deployed:

- A regular [TON HTTP API](https://github.com/toncenter/ton-http-api) is running on a separate server allowing to read data from the blockchain.

- A special service https://github.com/sonofmom/tps-backend that reads metrics every 10 seconds from the TON HTTP API and store them in its database is launched on a separate server.

- Static webpage hosted on GitHub - Dashboard UI https://github.com/ton-blockchain/performance-test/tree/main that requests metrics from the previous service by REST HTTP API and displays it on the page.


#### Third party auditors

[CertiK](https://www.certik.com/), Web3's leading auditors, will participate in this performance test. 

CertiK auditors have access to all test network nodes as well as lite servers. During the show, third-party auditors take independent measurements.
