# FAQ

## Why Keep The L1 Account Trie?

There are several reasons to keep it:

*   The size of the L1 account trie is relatively small, constituting only around 4% of the L2 storage trie on BSC as of the end of 2022.
*   The L1 account trie contains crucial information about user accounts, such as their balance and nonce. If users were required to revive their accounts before accessing their assets, it would significantly impact their experience.
*   By retaining the L1 account trie, the witness verification process can be much simpler.

## Why Not Create A New L2 Storage Trie?

In this proposal, the trie skeleton will be kept in a new epoch. There are other approaches 

which will generate a new trie tree from scratch at the start of a new epoch. Although they

provide a comprehensive solution for state expiry, there are still two unsolved issues to address: account resurrection conflict and witness size. Additionally, they would have a significant impact on the ecosystem and rely on other infrastructure, such as address extension and Verkle Tree.

By keeping the skeleton of the trie, it would be much easier to do witness verification and have less impact on the current ecosystem.

## Reasonable Epoch Period

The state will expire if it has not been accessed for at least 1 epoch or at most 2 epochs. On average, the expiry period is 1.5 epochs. If we set the epoch period to represent 2/3 of a year, then the average state expiry period would be one year, which seems like a reasonable value.

## What is the shadow tree?

A shadow tree is a shadow mapping to the MPT and can be accessed directly by the MPT. Each MPT node corresponds to its shadow node. For example, BranchNode corresponds to a ShadowBranchNode and ExtensionNode corresponds to a ShadowExtensionNode. In ShadowBranchNode, it contains an epoch map that will indicate if its child node is expired.

## What is Partial Revive?

When a high-level layer is revived, the actual witness depth may decrease If it is 2 or even 1, only leaf node witnesses are included, and the witness size is the smallest.

![](https://t25652588.p.clickup-attachments.com/t25652588/4852292d-77e3-424c-b050-a35e6b8181e3/image.png)

When `Witness[0]`, `Witness[1]`, `Witness[5]` revive `state C` on chain, you only provide `Witness[6]` to revive `state D` .

## How about witness size?

It depends on the unrevived Trie path, and revive by Partial Revive Mechanism. This is a reference to restore the witness size of a 7-layer Trie:

*   Max Witness, depth 4, size 1451 Bytes, gas 24740;
*   Min Witness, depth 1, size 42 Bytes, gas 1300;
*   Avg Witness, depth: 2, size 319 Bytes, gas 6012;

## What is Witness Format?

```plain
type WitnessList []ReviveWitness 
type ReviveWitness struct {
	WitnessType byte              // only support Merkle Proof for now
	Data        []byte            // witness data, it's rlp format
	cache       ReviveWitnessData `rlp:"-" json:"-"` // cache if not encode to rlp or json, it used for performance
}
type StorageTrieWitness struct {
	Address   common.Address // target account address
	ProofList []MPTProof     // revive multiple slots (same address)
}
type MPTProof struct {
	RootKeyHex []byte   // prefix key in nibbles format, max 65 bytes.
	Proof      [][]byte // list of RLP-encoded nodes
}
type MPTProofNub struct {
	n1PrefixKey []byte // n1's prefix hex key, max 64bytes
	n1          node
	n2PrefixKey []byte // n2's prefix hex key, max 64bytes
	n2          node
}
```

## How to generate Witness?

You can just call `eth_estimateGasAndReviveState` , it will return `ReviveStateTx` 's WitnessList and Gas, you just send tx to network.

  

Here is a diagram:

![](https://t25652588.p.clickup-attachments.com/t25652588/37594308-212a-4933-aa9d-f14a966e21ea/image.png)

## When is the contract slot expired?

It will expire only if it has not been accessed on the last epoch, and the slot that is always accessed will never expire. Attention, this rule is for contract slot, account data will never expire.

  

By default, an epoch is 2/3 years.

## Validator storage size compared to before?

Due to the addition of metadata to mark expired information, ArchiveNode will have a certain increase in storage scale compared with before.

  

For FullNode, because most of the historical data is not saved, and the expired data of more than 2 epochs will be expired at the same time, the storage requirement is greatly reduced, and the specific saving depends on the activity level of the chain.

  

However, most of the state of many inactive historical contracts will be pruned.

## Compare ETH's state expiry proposal

Ethereum’s solution is more inclined toward Statelessness. Of course, there are also state expiry studies, but whether it is Statelessness or state expiry, it poses a huge challenge to current ecology and historical compatibility, such as address extension, verkle tree, two-tree migration, and duplication, witness resolve state conflicts, etc.

  

However, based on the current BSC scheme, it maximizes compatibility with the ecological and historical states. There is no expired account state and no address change. It only adds a main RPC API and a state expiration certificate, making ecological adaptation easier.

  

## Why need two hard fork?

The advantage of using two hard forks is that the time to enter Epoch1 and Epoch2 can be controlled separately, and when enter Epoch2, there will appear expired state, during which we can continue to optimize and fix discovered bugs.

![](https://t25652588.p.clickup-attachments.com/t25652588/51d93844-a9f9-4daf-904b-1959d8525461/image.png)

Of course, this design can freely control the time to enter Epoch1 or Epoch2. When everything is ready, directly enable hard fork2 to enter Epoch2 without waiting too long.

  

## Does legacy Transaction still works?

Yes, legacy Transaction still works, but users must always check if the legacy transaction will access expired state.

  

For transaction that only involves balance transfer, it can be done as usual. because of the account data is always available.

  

If there iis any expired state, New Transaction Type, ReviveStateTx could revive state from witness, and execution tx as usual.

# All Diagrams

Diagram 1![](https://t25652588.p.clickup-attachments.com/t25652588/15f230d1-519a-4c95-a207-066aaa9447fe/image.png)

  

Diagram 2

![](https://t25652588.p.clickup-attachments.com/t25652588/10b31802-cbeb-4a5f-94a7-fb16f2e9f6b0/image.png)

Diagram 3

![](https://t25652588.p.clickup-attachments.com/t25652588/6691b9bd-5900-44d1-8df6-0b81158d15b4/image.png)

Diagram 4

![](https://t25652588.p.clickup-attachments.com/t25652588/f1888d04-989f-47e9-98d4-a044cb897894/image.png)

Diagram 5

![](https://t25652588.p.clickup-attachments.com/t25652588/c5d8664b-073d-49ad-80c6-dfbd9b19a738/image.png)

Diagram 6

![](https://t25652588.p.clickup-attachments.com/t25652588/07f61c24-5b91-4e94-b2ff-dc02967fd081/image.png)

Diagram 7

![](https://t25652588.p.clickup-attachments.com/t25652588/a29e8419-727d-4141-8a27-dca3ce9cc4a3/image.png)

Diagram 8

![](https://t25652588.p.clickup-attachments.com/t25652588/457b5a6e-fb47-44c0-af16-5a8641669cdf/image.png)

Diagram 9

![](https://t25652588.p.clickup-attachments.com/t25652588/dde48460-2502-495c-92e1-d8810c86da69/image.png)

Diagram 10

![](https://t25652588.p.clickup-attachments.com/t25652588/dee9297d-5db9-4cf6-8f7b-ecb17fb5d15f/image.png)

Diagram 11

![](https://t25652588.p.clickup-attachments.com/t25652588/bec72141-54ab-4b09-bb9e-c17fdd11aef3/image.png)

Diagram 12

![](https://t25652588.p.clickup-attachments.com/t25652588/05d63d7a-10bf-49b3-838c-3d0d6c8648b7/image.png)

Diagram 13

![](https://t25652588.p.clickup-attachments.com/t25652588/f0e578ad-85ff-4f77-ab8f-5840070ba518/image.png)

Diagram 14

![](https://t25652588.p.clickup-attachments.com/t25652588/4d5488a5-0d60-4426-a23f-a2fb07d5dc53/image.png)

Diagram 15

![](https://t25652588.p.clickup-attachments.com/t25652588/cfa509f0-9826-40ee-8ab5-5c9a7dc2eed2/image.png)

Diagram 16

![](https://t25652588.p.clickup-attachments.com/t25652588/75946071-c327-4a53-924e-abcd61287fc0/image.png)

Diagram 17

![](https://t25652588.p.clickup-attachments.com/t25652588/9ae45438-d1bb-4f36-9795-40dab0c9d3e7/image.png)

Diagram 18

![](https://t25652588.p.clickup-attachments.com/t25652588/2386e635-4ed3-4b9b-ac55-51872273bb7d/image.png)

Diagram 19

![](https://t25652588.p.clickup-attachments.com/t25652588/59eda832-9d68-4926-9949-6c0b0ded1bb2/image.png)

Diagram 20

![](https://t25652588.p.clickup-attachments.com/t25652588/35796f3b-9f70-4dd1-87f4-d70a250c3629/image.png)

Diagram 21

![](https://t25652588.p.clickup-attachments.com/t25652588/ca433e58-ea0c-45dc-abd3-d5e1cc21827e/image.png)

Diagram 22

![](https://t25652588.p.clickup-attachments.com/t25652588/83ecf5ac-db4c-4757-be3e-9ffe2c78e8e0/image.png)

Diagram 23

![](https://t25652588.p.clickup-attachments.com/t25652588/e56a56f9-19b5-42a0-a668-23bbb40c19ff/image.png)