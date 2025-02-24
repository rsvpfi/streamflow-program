StreamFlow
==========

This repository contains the Solana program source code.

It is laid out as a standard crate, and the program code can be found
in [src/lib.rs](src/lib.rs).

![Build and tests](https://github.com/streamflow-finance/streamflow-program/actions/workflows/rust-tests.yml/badge.svg)

Other **StreamFlow Finance** resources:
- [Platform Demo](https://www.youtube.com/watch?v=7HWzcxu-De0)
- [Slides](https://streamflow.finance/public/streamflow_slides.pdf)
- [Website](https://streamflow.finance)
- [App](https://app.streamflow.finance) ([GitHub repository](https://github.com/streamflow-finance/streamflow-app))

Deployment
----------

* On Solana Mainnet, the program is not yet deployed.
* On Solana Devnet, the program ID is:
  `2DvvSEde36Ch3B52g9hKWDYbfmJimLpJwVBV9Cknypi4`

To verify the deployed program corresponds to this source code, have
a look at the [verification](verification.md) document and the
[verify_deployment.sh](verify_deployment.sh) shell script.


Usage
-----

See [cli/strfi.js](cli/strfi.js) and [examples/strfi.rs](examples/strfi.rs)
to get an understanding.

### `initialize_stream`

This instruction is used to initialize the stream, and save the data
and lock the funds for streaming on a given account.

Note that the implementation sends `DEFAULT_TARGET_LAMPORTS_PER_SIGNATURE`
immetiately to the recipient, so they don't have to have previous funds
in order to issue a withdraw instruction in the future.

* Accounts:
    * Alice (Sender) (signer, writable)
    * Bob (Recipient) (writable)
    * PDA (Account where funds will be locked) (signer, writable)
    * Solana System Program

* Instruction data:
    * `instruction` (1 byte, u8) (Should be `0` for `initialize_stream`
    * `start_time` (64 bytes, u64) (Unix timestamp when funds start to be unlocked)
    * `end_time` (64 bytes, u64) (Unix timestamp when all funds should be unlocked)
    * `amount` (64 bytes, u64) (Amount of lamports to lock and stream)

* Data saved in the PDA account:
    * `start_time` (64 bytes, u64)
    * `end_time` (64 bytes, u64)
    * `amount` (64 bytes, u64)
    * `withdrawn` (64 bytes, u64) (Amount that has been withdrawn so far)
    * `sender` (32 bytes, u8 array) (Alice/Sender's public key)
    * `recipient` (32 bytes, u8 array) (Bob/Recipient's public key)


### `withdraw_unlocked`

This instruction is used by the stream recipient, and will transfer
a given amount of lamports, if unlocked, from the stream account to
the caller.

If the requested amount if lamports is 0 (zero), then all unlocked
funds will be withdrawn.

* Accounts:
    * Bob (Recipient) (signer, writable)
    * PDA (Account where the funds are locked) (writable)
    * Rent collector (Hardcoded address where the remaining rent is sent
      after a successful stream) (writable)

* Instruction data:
    * `instruction` (1 byte, u8) (Should be `1` for `withdraw_unlocked`)
    * `amount` (64 bytes, u64) (Amount of lamports to potentially withdraw)


### `cancel_stream`

This instruction is used by the stream initializer, and will cancel
the given stream, returning all locked funds to the caller and
purging the account.

* Accounts:
    * Alice (sender) (signer, writable)
    * Bob (recipient) (writable)
    * PDA (account where funds are locked) (writable)

* Instruction data:
    * `instruction` (1 byte, u8) (Should be `2` for `cancel_stream`)


License
-------

StreamFlow Rust code is licensed [AGPL-3](LICENSE).

<!-- commit on 2024-04-25 -->

<!-- commit on 2024-05-04 -->

<!-- commit on 2024-06-30 -->

<!-- commit on 2024-06-25 -->

<!-- commit on 2024-08-20 -->
