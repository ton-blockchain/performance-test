# TON Performance Test

On October 31, we proved that TON is the fastest and most scalable blockchain in the world.

During public performance testing on **256** validator nodes, a speed of **104 715** transactions of smart contract execution per second was set. CertiK, the Web3's leading auditor, participated in the event as an independent party and confirmed the results.

The most impressive thing is that the result is far from the limit. TON can handle millions of transactions per second if there are enough validator nodes in the network.

Read the full recap of the event: https://blog.ton.org/100000-transactions-per-second-ton-sets-the-world-record-on-its-first-performance-test

Watch the event: https://www.youtube.com/live/jWWl1sLGY7s?feature=share

Dashboard: https://ton-blockchain.github.io/performance-test/ (https://live.ton.org)

CertiK report: https://www.certik.com/resources/blog/7KVtBkHfJkcj0U6u0kKtPe-how-certik-verified-tons-tps-results

## Tech Details

### Separate testnet on 256 validator nodes

256 cloud servers (`ecs.c6.6xlarge`, `ubuntu_22_04_x64_20G_alibase_20230613.vhd`) rented in Alibaba Cloud for validator nodes.

A separate testnet has been launched.

### Target network configuration

Only one workchain. The network was split into 512 shardchains.

### Validators software

Validator nodes work on the upcoming version of TON, which after thorough testing will be release on TON mainnet.

This update is called "Accelerator" and contains a number of improvements and optimizations. 

Source code - https://github.com/ton-blockchain/ton/tree/accelerator 

Commit hash - `3466631f620a5227fda69fb175c019fed82665c6`

During the test, binaries downloaded from GitHub Autobuild are used - https://github.com/ton-blockchain/ton/actions/runs/6575234473

Binary checksum obtained by `sha256sum` - `aeddc5c4ea7f09299590351f5575bc423f8a3bab58017b9dfa5e4e2bf4d0b731`

Autobuild builds the source code on GitHub side from the "Accelerator" branch.

### Smart contract generating load

A special smart contract generating transactions has been developed.

Source code - https://github.com/ton-blockchain/benchmark-contracts

Commit hash - `3355860b97e22693b710ae1d2ac04496b8cf21ad`

A smart contract creates child smart contracts that send messages to each other, generating a load.

The design of a smart contract is a bit like the design of a Jetton contract.

Another feature of the smart contract is that child contracts send to the parent the number of transactions they have made, so transactions are counted on-chain.

### TPS counter of complex transactions

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

### Metrics and Dashboard

To display metrics of performance test for the user, the following infrastructure is deployed:

- A regular [TON HTTP API](https://github.com/toncenter/ton-http-api) is running on a separate server allowing to read data from the blockchain.

- A special service https://github.com/sonofmom/tps-backend that reads metrics every 10 seconds from the TON HTTP API and store them in its database is launched on a separate server.

- Static webpage hosted on GitHub - Dashboard UI https://github.com/ton-blockchain/performance-test/tree/main that requests metrics from the previous service by REST HTTP API and displays it on the page.

### Third party auditors

[CertiK](https://www.certik.com/), Web3's leading auditors, participated in test and verified the results.

CertiK auditors had access to all test network nodes as well as lite servers. During the show, third-party auditors took independent measurements.

CertiK report: https://www.certik.com/resources/blog/7KVtBkHfJkcj0U6u0kKtPe-how-certik-verified-tons-tps-results

### Public dump of test network

Public dump of the entire test network on which the performance test was conducted:

HTTP API: https://record.toncenter.com/

Dump: https://dump.ton.org/dumps/record_dump.tar.lz

Blockchain explorer: http://record-explorer.toncenter.com/

Global config: https://ton-blockchain.github.io/performance-test/record.global.config.archive-proxy.json

# FAQ

### Why wasn't the test conducted on the mainnet?

Generating that load on the main network would require a lot of money to pay network fees.

### About Layer2/Layer3/Rollups/etc.

We believe that Layer 2 solutions as well as ZK solutions can be useful for solving some specific problems.

That's why we are working on creating Layer 2 for TON - [TON Payment Network](https://ton.org/roadmap?filterBy=ton_payment), and also made [TVM upgrade](https://blog.ton.org/the-most-significant-tvm-update-so-far-extended-cryptography-arbitrary-precision-arithmetic-and-new-instructions) on the basis of which it is possible to build ZK on TON.

However, we do not share the opinion that these technologies will solve absolutely all problems in the world, simply because no technology is a silver bullet.

In any case, these technologies require a Layer 1, and we believe that Layer 1 should be as scalable, performant and universal as possible.

### Radix is faster!

Radix DLT is not a blockchain, but a decentralized database optimized for financial transactions.

https://learn.radixdlt.com/article/what-is-a-dlt#:~:text=DLT%20stands%20for%20distributed%20ledger,Radix%20is%20called%20Radix%20DLT.

Since Radix doesn't have blocks, it is more logical to compare it with other distributed databases (such as MongoDb) and not with blockchains.

### SUI is faster!

We use the common meaning of "transaction" - some atomic action, such as account(s) state change
or the execution of a smart contract(s), the result of which is recorded to the block.

When SUI blockchain shows hundreds of thousands of transactions, it refers to transactions as operations, and a single atomic write to a block can contain dozens of such operations. At one operation per write, the speed in SUI drops to 10K TPS.

[SUI states](https://blog.sui.io/sui-performance-update/), “The TPS capacity measurement most consistent with Sui’s design, least application-dependent, and most practical to track, is the number of individual transactions within a Programmable Transaction Block executed per second. For this and future updates, all mentions and measurements of TPS follow this convention.”

### Elrond (MultiversX) is faster!

According to Elrond's team they had peak of 263,000 TPS in a testnet with 1,500 nodes at June 2020.

1) Unfortunately, the team did not provide any additional information other than one picture, which makes a full comparison difficult.

   TON performance test was conducted publicly in real time, all necessary documentation was provided, and the result was verified by a third-party auditor CertiK.

2) However, it can be assumed that the Elrond test count simple coin transfers, but not smart contract execution transactions, because smart contracts introduced in Elrond only in 2021 according to https://multiversx.com/about.

   In the TON performance test, transactions of the execution of complex smart contracts were calculated.

3) In TON, when adding nodes, performance increases. TON exceeded 100k TPS of execution of complex smart contracts on just 256 nodes, while according to the Elrond team, it took 1,500 nodes to achieve 260k TPS of simple coin transfers.

### Tezos is faster!

Tezos show large TPS using smart rollups. According to https://tezos.gitlab.io/alpha/smart_rollups.html "we will generally refer to the rollup under consideration as the Layer 2 on top of the Tezos blockchain, considered as the Layer 1".

TON showed its results by executing full-fledged Layer 1 transactions.

### L1X (Layer one X) is faster!

According to official website https://www.l1x.foundation/ "100K Transactions per second (theoretical)"

### <ANOTHER_NAME> is faster!

Unfortunately we don't have time to study all the projects in the world. We have studied blockchains from the top CoinMarketCap as well as the most frequently asked questions.

If any of the projects you are interested in have not been dealt with, we suggest you do the research yourself.

Usually the project is either not a blockchain, or is not a Layer 1 blockchain, or has not conducted a test, or carried out the test in the laboratory, has not provided any information about the test, or operates with terms that are not commonly used.

### Why do different sources have slightly different numbers - some have 104K TPS and some have 108K TPS?

Indeed, some sources wrote 108K+ TPS, others 104,715 TPS. Depends on the counting methodology.

The data in the dashboard was taken directly from the network and sampled by 10 seconds, so viewers got immediate results and saw dynamics. For the final result we choose conservative one minute timespan that is reasonable in this context: not too low to be unreliable, not too high to smooth the peak, and got 104,715 TPS.

### Why only 256 validators?

First and foremost, renting servers costs money. And even if you are willing to pay, datacenters are not interested in providing thousands of productive servers for a few hours. Clouds interested in long-term leases.

We are grateful to AliCloud for providing 256 servers for performance test, as it turned out to be enough for a TON to show the world record.

TON is one of the few blockchains where adding validators increases performance rather than decreases it. If the right opportunity to rent more servers comes up, we will definitely conduct an even bigger test.

### Were the servers hardware the best?

No, for performance test we took "ecs.c6.6xlarge" cloud server. There are better ones in the AliCloud lineup. Our idea was to show horizontal scaling, not vertical scaling.

The mainnet runs powerful dedicated servers rented for long term.

### Was each shard processed by one validator?

No, in showcase, each shard was processed by set of a few validators and these sets were frequently rotated. Such mechanism keep safety of the network.

### Why were there already 512 shards at the start of the TPS Showcase? Does dynamic sharding work?

Dynamic sharding works in both the mainnet and test network.

Splitting a network from 1 to 512 shards takes about 30 minutes. During this time the network is not idle, on the contrary, it becomes more and more productive with each shard. In order not to bore the viewers, we did it right before the live broadcast. On the live broadcast was the most interesting and highly loaded part and the peak of TPS.

The public dump of the test network contains the entire network history, including splitting to shards.

### Have you tried 1024 shards?

We chose network configuration which optimally utilise available hardware.

### Out of 512, how many shards were loaded?

We used special benchmark smartcontract system that loaded whole network more or less uniformly.

### How much space requires such payload? How many disks need to buy validator each day to validate the network?

During showcase network generated about 85 Gb of the data in an hour. But marvellous thing about TON is that sharding works not only for computation but for data too. That means that each node may store small part of the total state and history. Besides, in TON, for proper functioning node does not need to store full history: nodes automatically clean old unused data and thus there is no need for constant buying new HDDs or SSDs.