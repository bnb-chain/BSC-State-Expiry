# Solana

Solana has its own state expiry design, called `State Rent`. In this article, it will explain how Solana's `State Rent` works.

## Account

If the program needs to `store state between transactions,` it does so `using` _`accounts`_. Accounts are similar to files in operating systems such as Linux in that they may hold arbitrary data that persists beyond the lifetime of a program.

  

the account includes `metadata for the lifetime of the file.` That lifetime is expressed by a number of fractional native tokens called _`lamports`_.

*   Accounts are held in validator memory and pay ["rent"](https://docs.solana.com/developing/programming-model/accounts#rent) to stay there.
*   Each validator periodically scans all accounts and collects rent. Any account that drops to zero lamports is purged.
*   Accounts can also be marked [rent-exempt](https://docs.solana.com/developing/programming-model/accounts#rent-exemption) if they contain a sufficient number of lamports.

> The system may perform micropayments of fractional SOLs, which are called _lamports_. They are named in honor of Solana's biggest technical influence, [Leslie Lamport](https://en.wikipedia.org/wiki/Leslie_Lamport). A lamport has a value of 0.000000001 SOL.

  

```plain
#[repr(C)]
pub struct Account {
    pub lamports: u64, // lamports in the account
    pub data: Vec<u8>, // data held in this account
    pub owner: Pubkey, // the program that owns this account. If executable, the program that loads this account.
    pub executable: bool, // this account’s data contains a loaded program (and is now read-only)
    pub rent_epoch: Epoch, // the epoch at which this account will next owe rent
}
```

## Rent

The `fee for every Solana Account to store data` on the blockchain is called "_`rent`_". This _`time and space`_ `based fee is required to keep an account,` and therefore its data, alive on the blockchain since [clusters](https://docs.solana.com/cluster/overview) must actively maintain this data.

  

All Solana Accounts (and therefore Programs) are required to maintain a high enough LAMPORT balance to become [rent exempt](https://docs.solana.com/developing/intro/rent#rent-exempt) and remain on the Solana blockchain.

  

When an Account `no longer has enough LAMPORTS to pay its rent`, `it will be removed` from the network in a process known as [Garbage Collection](https://docs.solana.com/developing/intro/rent#garbage-collection).

  

> **Note:** Rent is different from [transactions fees](https://docs.solana.com/transaction_fees). Rent is paid (or held in an Account) to keep data stored on the Solana blockchain. Whereas transaction fees are paid to process [instructions](https://docs.solana.com/developing/programming-model/transactions#instructions) on the network.

  

### Rent Rate

The Solana rent rate is set on a network wide basis, primarily based on the set LAMPORTS _per_ byte _per_ year.

  

Currently, the rent rate is a `static amount` and stored in the [Rent sysvar](https://docs.solana.com/developing/runtime-facilities/sysvars#rent).

  

> Note: Rest assured that, should the storage rent rate need to be increased at some point in the future, steps will be taken to ensure that accounts that are rent-exempt before the increase will `remain rent-exempt afterwards`

  

### Rent exempt

Accounts that maintain `a minimum LAMPORT balance greater than 2 years worth of rent payments` are considered "_rent exempt_" and will not incur a rent collection.

  

> Note: Every time an account's balance is reduced, a check is performed to see if the account is still rent exempt. Transactions that would `cause an account's balance to drop below the rent exempt threshold will fail.`

  

## When pay Rent?

There are two timings of collecting rent from accounts: 

1. when referenced by a transaction, includes the transaction to `create the new account` itself, and it happens during the `normal transaction processing` by the bank as part of the load phase.
2. periodically once an epoch. exists to ensure to collect rents from stale accounts, which aren't referenced in recent epochs at all. It requires the `whole scan of accounts` and is spread over an epoch `based on account address prefix to avoid load spikes` due to this rent collection.

  

On the contrary, rent collection isn't applied to accounts that are directly manipulated by any of protocol-level bookkeeping processes including.

  

## Actual processing of collecting rent

If the account is in the exempt regime, `Account::rent_epoch` is simply updated to `current_epoch`.

  

If the account is non-exempt, the difference between the next epoch and `Account::rent_epoch` is used to calculate the amount of rent owed by this account (via `Rent::due()`). Any fractional lamports of the calculation are truncated. Rent due is deducted from `Account::lamports` and `Account::rent_epoch` is updated to `current_epoch + 1` (= next epoch).

  

Accounts whose balance is `insufficient` to satisfy the rent that would be due simply fail to load.

  

### Distribution of rent

A percentage of the rent collected is destroyed. The rest is distributed to validator accounts by stake weight, a la transaction fees, at the end of every slot.

  

## How to calculate Rent?

**Using the Solana rent command from the** [**Solana CLI**](https://docs.alchemy.com/docs/how-to-setup-your-solana-development-environment) **provides a simple approach to estimate rent costs.** You can view the rent per byte, per epoch, and the minimum amount required for an account to be rent-exempt by entering the size (in bytes) of your account.

![](https://t25652588.p.clickup-attachments.com/t25652588/2239de7d-99ed-4fe4-aca9-7fb8ede9f241/image.png)

  

## Account GC

Accounts that do not maintain their rent exempt status, or have a balance high enough to pay rent, are removed from the network in a process known as _garbage collection_. This process is done to help reduce the network wide storage of no longer used/maintained data.

  

You can learn more about [garbage collection here](https://docs.solana.com/implemented-proposals/persistent-account-storage#garbage-collection) in this implemented proposal.

  

### How to delete account?

When Anchor closes an account, it `transfers out all of the lamports from the account`, which means that the account is eventually `cleaned up by the runtime and can be reinitialized`.

  

## **How to Redeem Solana Storage Fees**

To reclaim Solana storage fees, developers and everyday Solana users can close accounts to receive their storage fees back. The simplest way to redeem Solana rent fees is using a consumer friendly tool like **Sol Incinerator** ([https://sol-incinerator.com/](https://sol-incinerator.com/)) to close unused program accounts in your wallet.

  

## Reference

*   [https://docs.rs/solana-sdk/latest/solana\_sdk/account/struct.Account.html](https://docs.rs/solana-sdk/latest/solana_sdk/account/struct.Account.html)
*   [https://docs.rs/solana-sdk/latest/solana\_sdk/account/struct.Account.html](https://docs.rs/solana-sdk/latest/solana_sdk/account/struct.Account.html)
*   [https://docs.solana.com/developing/programming-model/accounts](https://docs.solana.com/developing/programming-model/accounts)
*   [https://www.alchemy.com/overviews/how-to-calculate-rent-for-solana-programs](https://www.alchemy.com/overviews/how-to-calculate-rent-for-solana-programs#:~:text=Rent%20is%20the%20fee%20every,the%20amount%20of%20data%20stored)
*   [https://solana.stackexchange.com/questions/6202/can-a-closed-account-in-anchor-be-initialised-again](https://solana.stackexchange.com/questions/6202/can-a-closed-account-in-anchor-be-initialised-again)
*   [https://docs.solana.com/developing/intro/rent](https://docs.solana.com/developing/intro/rent)
*   [https://docs.solana.com/implemented-proposals/persistent-account-storage#garbage-collection](https://docs.solana.com/implemented-proposals/persistent-account-storage#garbage-collection)
*   [https://docs.solana.com/implemented-proposals/rent](https://docs.solana.com/implemented-proposals/rent)
