# Witness & Revive

The witness used to revive storage undergoes a series of transformations, starting from `WitnessList` to `MPTProofNub` . Here's the flow:

  

![](../assets/consensus-state-expiry/witness-and-revive.png)

1. When users send a revive transaction, it contains a **WitnessList** which is a list of **ReviveWitness**.
2. Each witness is decoded into **StorageTrieWitness** which contains a list of **MPTProof**.
3. Each **MPTProof** is processed to generate **MPTProofCache**, which consists of a list of **MPTProofNub**.
4. Each **MPTProofNub** is used to revive an individual storage trie node. This allows **both partial and full revive**.

