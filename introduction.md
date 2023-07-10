# Introduction

As part of the BSC community, NodeReal has proposed a state expiry scheme for BSC, aiming to reduce node storage requirements and increase overall decentralization. The state expiry scheme works in a way such that the expired storage state is removed from the blockchain, but can be revived by users through a new type of transaction. The development team has also built a Proof-of-Concept (POC), showing up to 89% reduction in storage trie size.

## The State Bloat Problem

State bloat refers to the phenomenon where the state of a blockchain network grows larger and more complex over time. It occurs as a result of accumulating transaction data and the need to store the current state of the blockchain network. A larger blockchain state results in higher hardware requirements, increased network latency, and performance degradation. Ultimately, this leads to an overall increase in centralization.

Due to the high volume of traffic, BSC's storage size grows rapidly. As of the end of 2022, a pruned BSC full node snapshot file is approximately 1.6TB in size, a 60% increase in just a year. To learn more about BSC's storage status, you may refer to [NodeReal's BSC Annual Storage Report 2023](https://nodereal.io/blog/en/bnb-smart-chain-annual-storage-report-2023/).

  

## Why State Expiry?

Since as early as 2015, there have been many ideas for tackling the state bloat problem. It started with blockchain state rent, proposed by Vitalik Buterin, which aims to introduce a renting mechanism where users have to pay a fee to maintain their data on the network. Then, the concept of statelessness is introduced such that only block producers need to store state data, whereas the other nodes can just verify the block using witnesses. At the same time, state expiry is proposed.

  

In general, state expiry refers to the mechanism designed to limit the growth of the blockchain's state database. When certain data is no longer used for a long period, it can be expired and removed from the blockchain network. At the same time, the data can be revived and added back to the blockchain by providing proof.

  

With the implementation of state expiry, unused state data is no longer stored. Hence, it reduces the hardware requirements for running a full node. This helps to lift the barrier to running an individual node, which increases the overall decentralization of a network.

  

## BSC's State Expiry Proposal

For the state expiry proposal, two BEPs have been proposed — BEP-206 and BEP-215.

### BEP-206

Proposal: [https://github.com/bnb-chain/BEPs/pull/206](https://github.com/bnb-chain/BEPs/pull/206)

  

![](https://t25652588.p.clickup-attachments.com/t25652588/928571f6-437b-42a4-9d5b-db581c43bed7/image.png)

BEP-206 proposes a hybrid mode state expiry with the addition of Shadow Tree and epoch in the storage tree. The expiry mechanism only works on storage state data (i.e. it will not affect EOA). The Shadow Tree is used to store the access record for the MPT tree, such that all untouched states in the last 2 epochs will expire. More technical details can be found in Private ([https://app.clickup.com/25652588/docs/revbc-25105/revbc-121925](https://app.clickup.com/25652588/docs/revbc-25105/revbc-121925)) .

  

### BEP-215

Proposal: [https://github.com/bnb-chain/BEPs/pull/215](https://github.com/bnb-chain/BEPs/pull/215)

  

BEP-215 proposes a new transaction type to support BEP-206. The new transaction type allows the ability to revive a storage state and execute as usual in the same transaction. More technical details can be found in Private ([https://app.clickup.com/25652588/docs/revbc-25105/revbc-123605](https://app.clickup.com/25652588/docs/revbc-25105/revbc-123605)) .

  

### ChangeLog

2023-01-18: First iteration of the state expiry proposal

2023-02-23: Second iteration of the state expiry proposal

2023-03-21: Third iteration of the state expiry proposal

2023-03-22: Development of state expiry POC begins

2023-05-15: Development of POC is concluded and shared within the internal teams

2023-06-13: Researching on Verkle Tree and how it can be integrated into BEP-206