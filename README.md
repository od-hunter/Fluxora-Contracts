# Fluxora Contracts

Soroban smart contracts for the Fluxora treasury streaming protocol on Stellar. Stream USDC from a treasury to recipients over time with configurable rate, duration, and cliff.

## What's in this repo

- **Stream contract** (`contracts/stream`) — Lock USDC, accrue per second, withdraw on demand.
- **Data model** — `Stream` (sender, recipient, deposit_amount, rate_per_second, start/cliff/end time, withdrawn_amount, status).
- **Status** — Active, Paused, Completed, Cancelled.
- **Methods (stubs)** — `init`, `create_stream`, `pause_stream`, `resume_stream`, `cancel_stream`, `withdraw`, `calculate_accrued`, `get_stream_state`.
- **Cancel semantics** — `cancel_stream` is valid only in `Active` or `Paused`; `Completed` and `Cancelled` return `InvalidState`.

Implementation is scaffolded; storage, token transfers, and events are left for you to complete.

## Tech stack

- Rust (edition 2021)
- [soroban-sdk](https://docs.rs/soroban-sdk) (Stellar Soroban)
- Build target: `wasm32-unknown-unknown` for deployment

## Local setup

### Prerequisites

- Rust 1.70+
- [Stellar CLI](https://developers.stellar.org/docs/tools/developer-tools) (optional, for deploy/test on network)

```bash
rustup target add wasm32-unknown-unknown
```

### Build

From the repo root:

```bash
cargo build --release -p fluxora_stream
```

WASM output is under `target/wasm32-unknown-unknown/release/fluxora_stream.wasm`.

### Test

```bash
cargo test -p fluxora_stream
```

(Add unit tests with `soroban-sdk` feature `testutils` when needed; see Stellar Soroban docs.)

### Deploy (after Stellar CLI setup)

```bash
stellar contract deploy \
  --wasm-file target/wasm32-unknown-unknown/release/fluxora_stream.wasm \
  --network testnet
```

Then invoke `init` with token and admin addresses, and use `create_stream`, `withdraw`, etc. as needed.

## Project structure

```
fluxora-contracts/
  Cargo.toml              # workspace
  contracts/
    stream/
      Cargo.toml
      src/
        lib.rs            # contract types and impl
        test.rs           # unit tests
```

## Accrual formula (reference)

- **Accrued** = `min((current_time - start_time) * rate_per_second, deposit_amount)`
- **Withdrawable** = `Accrued - withdrawn_amount`
- Before `cliff_time`: withdrawable = 0.

## Related repos

- **fluxora-backend** — API and Horizon sync
- **fluxora-frontend** — Dashboard and recipient UI

Each is a separate Git repository.
