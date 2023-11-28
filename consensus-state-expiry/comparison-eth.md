# Etherem

Under Ethereum's long-term roadmap, there are some specific components that are related to State Expiry.

### EIP-4444: Bound Historical Data in Execution Clients

Historical data is not essential for validating new blocks. Once a client has synchronized with the latest point in the blockchain, historical data is only retrieved when explicitly requested through JSON-RPC or during a chain synchronization attempt by a peer. EIP-4444 aims to reduce the disk requirements for users by pruning the historical data. By doing so, clients can eliminate the code responsible for processing historical blocks. This approach eliminates the need for execution clients to maintain code paths that handle each successive upgrade's cumulative changes.


### Weak Statelessness

Weak Statelessness is a concept where only block proposers are required to store the state. All other nodes would be able to verify blocks statelessly by downloading a witness that proves that the block is valid. This would allow for a significant reduction in the amount of data that most nodes need to store. To implement this feature, Verkle Trees need to be integrated so that the witness size is small enough.


### Portal Network

The Portal Network is a project that enables lightweight protocol access to resource-constrained devices. It can be used to support EIP-4444, as historical data can be fragmented and each data piece can be stored by the network participants.

  

### State Expiry

The vision of state expiry is similar for both Ethereum and BSC, but the architecture design differs. We will compare with [Vitalik's State Expiry EIP](https://notes.ethereum.org/@vbuterin/state_expiry_eip) published 2 years ago. Note that this EIP is still in its infant phase, it could be changed in the future.

  

As a brief introduction to the EIP, it proposes a scheme where a single state tree is replaced with a list of state trees. Each state tree represents approximately 8 months. Any state edits correspond to the current period’s state tree, and trees older than the most recent two are no longer stored by clients. If users wish to access an old state, witnesses need to be provided.

  

We will compare the similarities and differences between BEP-206 and the EIP.

  

## Similarities

*   Accessing old states require witnesses
*   Introduction of epochs to determine expiry status
*   States older than 2 epochs will no longer be stored in clients

  

## Differences

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

  

## Resources

*   [https://www.youtube.com/watch?v=jAX\_bgcESoc](https://www.youtube.com/watch?v=jAX_bgcESoc) [https://www.youtube.com/watch?v=YzL2GmxA6Wk](https://www.youtube.com/watch?v=YzL2GmxA6Wk)
*   [https://ethereum.github.io/trin/introduction/portal\_network.html#portal-network](https://ethereum.github.io/trin/introduction/portal_network.html#portal-network)
*   [https://notes.ethereum.org/@pipermerriam/r1zyBbmpj](https://notes.ethereum.org/@pipermerriam/r1zyBbmpj)
*   [https://github.com/ethereum/portal-network-specs](https://github.com/ethereum/portal-network-specs)
*   [https://archive.devcon.org/archive/watch/6/the-portal-network/?playlist=Devcon%206&tab=YouTube](https://archive.devcon.org/archive/watch/6/the-portal-network/?playlist=Devcon%206&tab=YouTube)
*   [https://eips.ethereum.org/EIPS/eip-4444](https://eips.ethereum.org/EIPS/eip-4444)[https://www.youtube.com/watch?v=SfDC\_qUZaos](https://www.youtube.com/watch?v=SfDC_qUZaos)
*   [https://ethereum-magicians.org/t/eip-4444-bound-historical-data-in-execution-clients/7450](https://ethereum-magicians.org/t/eip-4444-bound-historical-data-in-execution-clients/7450)[https://notes.ethereum.org/@ralexstokes/BJWd8saB9](https://notes.ethereum.org/@ralexstokes/BJWd8saB9)
*   [https://twitter.com/TimBeiko/status/1558182696592887809](https://twitter.com/TimBeiko/status/1558182696592887809)
*   [https://www.reddit.com/r/ethereum/comments/qzvsfq/impromptu\_technical\_ama\_on\_history\_expiry/](https://www.reddit.com/r/ethereum/comments/qzvsfq/impromptu_technical_ama_on_history_expiry/)
*   [https://twitter.com/lightclients/status/1462576116359569411](https://twitter.com/lightclients/status/1462576116359569411)
*   [https://github.com/tvanepps/EthereumDiscordGuidebook/blob/main/state-expiry/README.md](https://github.com/tvanepps/EthereumDiscordGuidebook/blob/main/state-expiry/README.md)
*   [https://notes.ethereum.org/@vbuterin/state\_expiry\_eip](https://notes.ethereum.org/@vbuterin/state_expiry_eip)
*   [https://notes.ethereum.org/@vbuterin/verkle\_and\_state\_expiry\_proposal](https://notes.ethereum.org/@vbuterin/verkle_and_state_expiry_proposal)
*   [https://notes.ethereum.org/@vbuterin/verkle\_tree\_eip](https://notes.ethereum.org/@vbuterin/verkle_tree_eip)
*   [https://hackmd.io/@vbuterin/state\_size\_management](https://hackmd.io/@vbuterin/state_size_management)
*   [https://hackmd.io/@vbuterin/state\_expiry\_paths](https://hackmd.io/@vbuterin/state_expiry_paths)
*   [https://ethresear.ch/t/resurrection-conflict-minimized-state-bounding-take-2/8739](https://ethresear.ch/t/resurrection-conflict-minimized-state-bounding-take-2/8739)
*   [https://www.youtube.com/watch?v=qQpvkxKso2E](https://www.youtube.com/watch?v=qQpvkxKso2E)
*   [https://medium.com/@mandrigin/regenesis-explained-97540f457807](https://medium.com/@mandrigin/regenesis-explained-97540f457807)
*   [https://notes.ethereum.org/@ipsilon/address-space-extension-exploration](https://notes.ethereum.org/@ipsilon/address-space-extension-exploration)
*   [https://ethereum-magicians.org/t/increasing-address-size-from-20-to-32-bytes/5485](https://ethereum-magicians.org/t/increasing-address-size-from-20-to-32-bytes/5485)
*   [https://ethresear.ch/t/the-stateless-client-concept/172](https://ethresear.ch/t/the-stateless-client-concept/172)
*   [https://github.com/ethereum/EIPs/issues/35](https://github.com/ethereum/EIPs/issues/35)