# Glory
This repository contains a Clarity smart contract that implements a sealed-bid, commit-reveal auction pattern aimed at awarding a contract in a Web3 context. It is designed to be tested with Clarinet (the Clarity testing framework) and to demonstrate a secure bidding flow.


**Files included**
- `contract.clar` — main Clarity contract implementing commit/reveal and winner selection.


**Design highlights**
- Commit phase: bidders submit a 32-byte SHA-256 commitment of `(amount || salt)`.
- Reveal phase: bidders reveal `amount` and `salt`. The contract verifies commitment and records the revealed bid.
- Finalize: owner finalizes and the highest revealed bidder is selected as winner.
- Withdraw: bidders can reclaim deposits (in a production contract this would transfer STX/FT).


**Security notes & production TODOs**
- Serialization: The example uses `buff-from-uint` as an illustrative helper. In production, use a deterministic serialization for packing `amount` + `salt` before hashing (e.g., fixed-width big-endian encoding + salt buffer) so that `sha256` matches off-chain commitments.
- STX transfers: Replace the deposit bookkeeping with SIP-010 or STX transfer calls to actually lock funds.
- Time/phase enforcement: Use block-height or unix-timestamp oracle (or the transaction block height) to programmatically enforce deadlines rather than manual `set-phase`.
- Gas/size limits: Commitments are 32 bytes; larger data increases cost. Consider off-chain IPFS storage for large attachments and commit to their hash.


**Testing with Clarinet**
1. Install Clarinet: `cargo install --locked clarinet` (see Clarinet docs for latest instructions).
2. Place `contract.clar` in the contracts directory of a Clarinet project.
3. Write Clarinet tests (TypeScript) to simulate commitments and reveals. Example pattern:
