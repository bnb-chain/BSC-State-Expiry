# Witness & Revive

The witness used to revive storage undergoes a series of transformations, starting from `WitnessList` to `MPTProofNub` . Here's the flow:

  

![](https://t25652588.p.clickup-attachments.com/t25652588/59eda832-9d68-4926-9949-6c0b0ded1bb2/image.png)

1. When users send a revive transaction, it contains a **WitnessList** which is a list of **ReviveWitness**.
2. Each witness is decoded into **StorageTrieWitness** which contains a list of **MPTProof**.
3. Each **MPTProof** is processed to generate **MPTProofCache**, which consists of a list of **MPTProofNub**.
4. Each **MPTProofNub** is used to revive an individual storage trie node. This allows **both partial and full revive**.

