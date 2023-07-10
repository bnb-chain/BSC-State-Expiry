# Polygon Miden

Polygon Miden is a ZK-optimized rollup with client-side proving, enabling concurrent transactions. Unlike BSC and Ethereum, Polygon Miden combines the account model, the UTXO model (as on Bitcoin), and ZK-proofs. This creates a concurrent state model, allowing multiple transactions to be processed in parallel.

In Polygon Miden's state model, there are 3 types of databases — Account, Note and Nullifier. Operators do not need to store the full state for Account and Note databases, but they need to store the full nullifier database for block verification. The nullifier database would grow linearly with the number of consumed transactions, and this causes the state bloat problem.

To mitigate the state bloat issue of the nullifier database, Polygon Miden adopts a solution that is similar to the state expiry scheme. It uses the concept of epochs, where a new nullifier database is created in each new epoch. The operator only stores the last two full trees and all historical nullifiers’ tree roots, minimizing the state bloat problem.


## Resources

*   [https://polygon.technology/blog/polygon-miden-ethereum-extended](https://polygon.technology/blog/polygon-miden-ethereum-extended)
*   [https://polygon.technology/blog/polygon-miden-state-model](https://polygon.technology/blog/polygon-miden-state-model?utm_source=twitter&utm_medium=social&utm_content=miden-state-model)
*   [https://polygon.technology/blog/polygon-miden-transaction-model-2](https://polygon.technology/blog/polygon-miden-transaction-model-2)
*   [https://www.youtube.com/watch?v=TEPY19-hie4](https://www.youtube.com/watch?v=TEPY19-hie4)