# Solution Comparison

## Ethereum

The vision of state expiry is similar for both Ethereum and BSC, but the architecture design differs. We will compare with [Vitalik's State Expiry EIP](https://notes.ethereum.org/@vbuterin/state_expiry_eip) published 2 years ago. Note that this EIP is still in its infant phase, it could be changed in the future.

  

As a brief introduction to the EIP, it proposes a scheme where a single state tree is replaced with a list of state trees. Each state tree represents approximately 8 months. Any state edits correspond to the current period’s state tree, and trees older than the most recent two are no longer stored by clients. If users wish to access an old state, witnesses need to be provided.

  

We will compare the similarities and differences between BEP-206 and the EIP.

  

### Similarities

*   Accessing old states require witnesses
*   Introduction of epochs to determine expiry status
*   States older than 2 epochs will no longer be stored in clients

  

### Differences

|  | BEP-206 | State Expiry EIP |
| ---| ---| --- |
| Tree Structure | Merkle Patricia Tree + Shadow Tree (Verkle Tree + Shadow Tree can be supported) | Verkle Tree |
| Number of Trees | 1 | Depending on the number of epochs, which is roughly 1 tree every 8 months |
| Witness Type | Merkle Proof (Verkle Proof can be supported) | Verkle Proof |
| Witness Size | Large (Small if use Verkle Tree) | Small |
| Expire Accounts | No | Yes |
| Revive Logic | Full witness or partial witness from expired node | History witness + no conflict proofs |
| Metadata | Yes (Shadow Nodes) | No |
| Address Space Extension | No | Yes |

  

The biggest advantage of the EIP is having a much smaller witness size due to the usage of the Verkle tree. However, there are some disadvantages to this EIP. Firstly, since Address Space Extension (ASE) is introduced, it is computationally heavy to provide the ranged witness for each epoch. Beyond that, since each epoch has its isolated tree, the I/O cost to access the trees could affect the node performance. Lastly, there's data redundancy. Since a node stores the most recent two trees, both trees could have the same number of nodes in the worst-case scenario, increasing storage cost by 2x.

  

  

### Resources

*   [https://twitter.com/TimBeiko/status/1558182696592887809](https://twitter.com/TimBeiko/status/1558182696592887809)
*   [https://www.reddit.com/r/ethereum/comments/qzvsfq/impromptu\_technical\_ama\_on\_history\_expiry/](https://www.reddit.com/r/ethereum/comments/qzvsfq/impromptu_technical_ama_on_history_expiry/)
*   [https://notes.ethereum.org/@vbuterin/state\_expiry\_eip](https://notes.ethereum.org/@vbuterin/state_expiry_eip)
*   [https://notes.ethereum.org/@vbuterin/verkle\_and\_state\_expiry\_proposal](https://notes.ethereum.org/@vbuterin/verkle_and_state_expiry_proposal)
*   [https://notes.ethereum.org/@vbuterin/verkle\_tree\_eip](https://notes.ethereum.org/@vbuterin/verkle_tree_eip)
*   [https://hackmd.io/@vbuterin/state\_size\_management](https://hackmd.io/@vbuterin/state_size_management)
*   [https://hackmd.io/@vbuterin/state\_expiry\_paths](https://hackmd.io/@vbuterin/state_expiry_paths)
*   [https://ethresear.ch/t/resurrection-conflict-minimized-state-bounding-take-2/8739](https://ethresear.ch/t/resurrection-conflict-minimized-state-bounding-take-2/8739)
*   [https://medium.com/@mandrigin/regenesis-explained-97540f457807](https://medium.com/@mandrigin/regenesis-explained-97540f457807)
*   [https://notes.ethereum.org/@ipsilon/address-space-extension-exploration](https://notes.ethereum.org/@ipsilon/address-space-extension-exploration)
*   [https://ethereum-magicians.org/t/increasing-address-size-from-20-to-32-bytes/5485](https://ethereum-magicians.org/t/increasing-address-size-from-20-to-32-bytes/5485)

  

## Solana

Solana has a similar way of tackling the state bloat problem called **State Rent**.

  

In Solana, the state is stored using accounts. An account includes metadata for the lifetime of the file, and it is expressed by a number of fractional native tokens called _lamports_.

  

The fee for every Solana Account to store data on the blockchain is called _rent_. Since Solana [clusters](https://docs.solana.com/cluster/overview) must actively maintain this data, there is a time and space-based fee required to keep an account and its data alive on the blockchain. Accounts pay ["rent"](https://docs.solana.com/developing/programming-model/accounts#rent) to hold their data in validators' memory. Each validator occasionally collects rent from all accounts. If an account is unavailable to pay rent (i.e. zero lamports), it is purged and removed from the network in a process known as [garbage collection](https://docs.solana.com/developing/intro/rent#garbage-collection).

  

However, an account can become [rent exempted](https://docs.solana.com/developing/intro/rent#rent-exempt) if the account maintains a high enough lamport balance. A rent-exempted account does not have to pay rent and can still remain on the blockchain.

  

To summarize, by ensuring that accounts pay certain amount of fees to be stored on Solana, it reduces the amount of redundant data and ensure that accounts on the blockchain are the active ones.

  

### Resources

*   [https://docs.rs/solana-sdk/latest/solana\_sdk/account/struct.Account.html](https://docs.rs/solana-sdk/latest/solana_sdk/account/struct.Account.html)
*   [https://docs.rs/solana-sdk/latest/solana\_sdk/account/struct.Account.html](https://docs.rs/solana-sdk/latest/solana_sdk/account/struct.Account.html)
*   [https://docs.solana.com/developing/programming-model/accounts](https://docs.solana.com/developing/programming-model/accounts)
*   [https://www.alchemy.com/overviews/how-to-calculate-rent-for-solana-programs](https://www.alchemy.com/overviews/how-to-calculate-rent-for-solana-programs#:~:text=Rent%20is%20the%20fee%20every,the%20amount%20of%20data%20stored)
*   [https://solana.stackexchange.com/questions/6202/can-a-closed-account-in-anchor-be-initialised-again](https://solana.stackexchange.com/questions/6202/can-a-closed-account-in-anchor-be-initialised-again)
*   [https://docs.solana.com/developing/intro/rent](https://docs.solana.com/developing/intro/rent)
*   [https://docs.solana.com/implemented-proposals/persistent-account-storage#garbage-collection](https://docs.solana.com/implemented-proposals/persistent-account-storage#garbage-collection)
*   [https://docs.solana.com/implemented-proposals/rent](https://docs.solana.com/implemented-proposals/rent)

  

## Polygon Miden

Polygon Miden is a ZK-optimized rollup with client-side proving, enabling concurrent transactions. Unlike BSC and Ethereum, Polygon Miden combines the account model, the UTXO model (as on Bitcoin), and ZK-proofs. This creates a concurrent state model, allowing multiple transactions to be processed in parallel.

  

In Polygon Miden's state model, there are 3 types of databases — Account, Note and Nullifier. Operators do not need to store the full state for Account and Note databases, but they need to store the full nullifier database for block verification. The nullifier database would grow linearly with the number of consumed transactions, and this causes the state bloat problem.

  

To mitigate the state bloat issue of the nullifier database, Polygon Miden adopts a solution that is similar to the state expiry scheme. It uses the concept of epochs, where a new nullifier database is created in each new epoch. The operator only stores the last two full trees and all historical nullifiers’ tree roots, minimizing the state bloat problem.

  

### Resources

*   [https://polygon.technology/blog/polygon-miden-ethereum-extended](https://polygon.technology/blog/polygon-miden-ethereum-extended)
*   [https://polygon.technology/blog/polygon-miden-state-model](https://polygon.technology/blog/polygon-miden-state-model?utm_source=twitter&utm_medium=social&utm_content=miden-state-model)
*   [https://polygon.technology/blog/polygon-miden-transaction-model-](https://polygon.technology/blog/polygon-miden-transaction-model-2)
*   [https://www.youtube.com/watch?v=TEPY19-hie4](https://www.youtube.com/watch?v=TEPY19-hie4)
