# BSC State Expiry
## 📦 The State Bloat Problem

State bloat refers to the phenomenon where the state of a blockchain network grows larger and more complex over time. It occurs as a result of accumulating transaction data and the need to store the current state of the blockchain network. A larger blockchain state results in higher hardware requirements, increased network latency, and performance degradation. Ultimately, this leads to an overall increase in centralization.

Due to the high volume of traffic, BSC's storage size grows rapidly. As of the end of 2022, a pruned BSC full node snapshot file is approximately 1.6TB in size, a 60% increase in just a year. To learn more about BSC's storage status, you may refer to [NodeReal's BSC Annual Storage Report 2023](https://nodereal.io/blog/en/bnb-smart-chain-annual-storage-report-2023/).

## ❓ Why State Expiry?

Since as early as 2015, there have been many ideas for tackling the state bloat problem. It started with blockchain state rent, proposed by Vitalik Buterin, which aims to introduce a renting mechanism where users have to pay a fee to maintain their data on the network. Then, the concept of statelessness is introduced such that only block producers need to store state data, whereas the other nodes can just verify the block using witnesses. At the same time, state expiry is proposed.

In general, state expiry refers to the mechanism designed to limit the growth of the blockchain's state database. When certain data is no longer used for a long period, it can be expired and removed from the blockchain network. At the same time, the data can be revived and added back to the blockchain by providing proof.

With the implementation of state expiry, unused state data is no longer stored. Hence, it reduces the hardware requirements for running a full node. This helps to lift the barrier to running an individual node, which increases the overall decentralization of a network.

## 💡 Idea 1: State Expiry with Consensus
The first state expiry PoC that was implemented is a consensus version of state expiry. The design proposes the inclusion of new tree structure alongside the storage MPT called ShadowTree. ShadowTree is used to store and record the state epoch of the storage trie nodes. 

Check out more details [here](./consensus-state-expiry/README.md).

## 💡 Idea 2: State Expiry without Consensus
The second state expiry PoC that was implemented is a non-consensus version of state expiry. The design proposes a new way of running full node which includes removing expired state from the local disk. To revive an expired state, a proof can be obtained from a remoteDB via a new RPC call. 

Check out more details [here](./non-consensus-state-expiry/README.md).