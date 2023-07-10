# State Expiry
Storage presents a significant challenge for many blockchains, the large storage size can cause several side effects on the chain. 
There are several simple ideas to solve the state explosion, e.g., rent accounts, state expiry, and statelessness. After considering ecological compatibility, feasibility, and other factors, BSC mainly adopts the state expiry method. In this method, users access a state, but the state that has not been used for a pre-determined amount of time gets pruned.
The design documents refer to [BEP-206](https://github.com/bnb-chain/BEPs/pull/206) and [BEP-215](https://github.com/bnb-chain/BEPs/pull/215).


## Table of Content
<!--ts-->
- [Introduction](introduction.md)
- [Public DevNet](public-devnet.md)
- [Run State Expiry DevNet](run-devnet.md)
- [Comparison with Other Solutions](solution-comparison.md)
  + [Ethereum](comparison-eth.md) 
  + [Solana](comparison-sol.md) 
  + [Polygon Miden](comparison-polygon.md) 
- Core Concepts
  + [Hard Fork and State Epoch](hard-fork-and-state-epoch.md) 
  + [MPT and Shadow Tree](mpt-and-shadow-tree.md) 
  + [Snapshot](snapshot.md) 
  + [EVM and StateDB](evm-and-statedb.md) 
  + [ReviveStateTx](revive-state-tx.md) 
  + [Witness and Revive](witness-and-revive.md) 
  + [Verkle Tree](verkle-tree.md) 
  + [Prune](prune.md) 
  + [Partial Revive](partial-revive.md) 
  + [Estimate Witness](estimate-witness.md) 
- [Impact on Ecosystem](impact-on-ecosystem.md) 
- [Data Availability Layer](data-availability-layer.md) 
- [POC Progress](poc-progress.md)
  + [Storage and Witness Analysis](storage-and-witness-analysis.md)  
  + [Changelog](changelog.md) 
- [FAQs](faqs.md) 
<!--te-->