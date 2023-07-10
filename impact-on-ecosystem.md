# Impact on Ecosystem

The State Expiry solution has changed the storage paradigm of on-chain data, and users no longer have to pay for permanent storage, which will inevitably affect the entire ecological tools and users.

## LightClient

Light Client is compatible in most scenarios. When querying get\_proof, in order to indicate to the user which states have expired, it is necessary to provide the proof of Shadow Tree at the same time.

## Sync

Sync has changed a lot. Currently, BSC uses Snap Sync the most, which is widely used by Full Node. Since Snap Sync needs to rebuild Trie, after State Expiry is enabled, ShadowTree also needs to be transmitted on the network and rebuilt locally.

  

For Full Sync, there are not many changes, because Full Sync can always be executed through blocks to complete chain synchronization, and at the same time build the correct Trie and ShadowTree locally.

## Wallet

The wallet is the user entrance. Due to the introduction of new transaction types, ReviveStateTx, and new RPC APIs, such as eth\_estimateGasAndReviveState, the existing wallet needs to support the generation of Witness in the expired state and send ReviveStateTx.

## SDK

The SDK is used to enrich the development tool chain of BSC, allowing more developers to access the ecosystem. All SDKs must support all new RPC APIs and new transaction types.

## RPC Provider

RPC Provider is a service commonly used by users for fast chain query and Gas estimation. Due to the introduction of new transaction types, ReviveStateTx, and new RPC APIs, such as `eth_estimateGasAndReviveState`, it is necessary to support all new APIs and new transaction types.

## User Impact

User experience to interact with a DApp may be hindered if the smart contracts are expired.

  

It's not intuitive for an average user to perform a revive operation unless a third-party provider can provide this service (e.g. BNB Greenfield, archive nodes).

