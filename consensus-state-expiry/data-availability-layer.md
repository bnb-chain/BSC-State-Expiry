# Data Availability Layer

The Data Availability (DA) Layer allows users to be confident that the data required to validate blocks is available to network participants.

  

Today, all state is saved in Layer1, and you can verify it yourself by synchronizing blocks. However, after State Expiry is enabled, most Full Nodes only retain the state of the last 1~2 years.

  

If you need to access earlier data, you need a witness. In order to ensure that all users can effectively generate witnesses and access historical states, it is necessary to introduce DA Layer, and support state witness service.

## Archive Node

Simply using Archive Node has the following problems:

1. Archive Node is a collection of historical data, which can solve the problem of historical state access, but it cannot be promised by the entire network, that is to say, it is maintained by a third party and may be an unstable storage method;
2. Archive Node requires huge storage, which severely limits the possibility of operating Archive Node and will lead to centralization;

## BNB GreenField

Using BNB GreenField is a better solution, which promises that the historical state of all users can be restored. At the same time, due to the large-scale storage advantages of the GreenField platform, the storage and verification costs of BSC state data are greatly reduced.

  

Currently, GreenField is still in the development stage, we will continue to follow up and use GreenField as the DA Layer of BSC when appropriate.
