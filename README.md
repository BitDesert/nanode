# Nano Client

Dead simple, promise-based client for interacting and building services on top of the NANO network, a next-generation cryptocurrency created by Colin LeMahieu with nearly instant transactions and no fees. [Learn more on the official repo](https://nanode.co/node-api) ⚡️

If you've worked with NANO before, you probably have experienced it's learning curve. The community is amazingly helpful and growing fast,
but the documentation and guides to working with it currently leave a lot to be desired.

This library is designed to get anyone, even a total beginner, up and running building services on NANO in just a few minutes. At it's core,
this package is a wrapper around the official [RPC protocol](https://github.com/nanocurrency/raiblocks/wiki/RPC-protocol) that does a few things:

    1. eliminates some of the idiosyncracies and quirks with the RPC API,
    2. exposes some top-level methods like `Nano.send()` and `Nano.recieve()`, so you don't have to know about creating, signing, publishing, etc.

Want to build with NANO, but not running a full node? [Sign up for node access and get your API key at nanode.co](https://nanode.co/node-api)

## Install

`npm install nanode`

## Usage

This library is built with TypeScript, and I highly reccommend you take advantage of the Intellisense. All fields on requests and responses for the RPC are strings - and for now, the same is true for this library.

The full code for these snippets can be found /examples directory

### Connect

Initiate the client with your API key and a valid RPC url. Optionally pass your address and private key to the constructor.

Not sure what your private key is? Call `nano.get_deterministic_key()` with your account's seed to get all relevant account information.

```typescript
//examples/init.ts
import Nano from 'nanode'
const nano = new Nano({
  api_key: process.env.API_KEY,
  url: 'https://api.nanode.co',
  origin_address: process.env.SENDER_WALLET, // wallet for the 'controlling' account
  origin_key: process.env.SENDER_WALLET_PRIVATE_KEY // key for the 'controlling' account
})
```

### Open account

Now that we're connected, we need to 'open' the account. Opening an account is a bit of a catch-22 - it requires any amount to be sent to the address from a funded account before account is actually open.

```typescript
//examples/open.ts

import Nano from './init'
//create a new key
const target = await Nano.key.create()

console.log(`Initializing account ${target.account} with funds..`)

//send new account init funds from our walet
const hash = await Nano.send('0.01', target.account)

//open block with send's hash
const hash = await Nano.open(
  hash,
  process.env.DEFAULT_REP,
  target.private,
  target.public
)
console.log(`Open block published with hash: ${hash}`)
```

### Send and receive

Sending and receiving are simple one liners. For receive, we have to pass in the hash of the block we're receiving

```typescript
//examples/send.ts, examples/receive.ts
import Nano from './init'

const hash = await Nano.send('0.01', process.env.RECIPIENT_WALLET)

return await Nano.receive(
  hash,
  process.env.RECIPIENT_WALLET_PRIVATE_KEY,
  process.env.RECIPIENT_WALLET
)
```

## Full list of methods

### Top-level calls

    - ```Nano.open```
    - ```Nano.send```
    - ```Nano.receive```
    - ```Nano.change```

### Account

All of these methods take a single account string or array of accounts:

    - ```Nano.account.get()```
    - ```Nano.account.balance()```
    - ```Nano.account.balances()```
    - ```Nano.account.block_count()```
    - ```Nano.account.frontiers()```
    - ```Nano.account.history()```
    - ```Nano.account.info()```
    - ```Nano.account.key()```
    - ```Nano.account.ledger()```
    - ```Nano.account.pending()```
    - ```Nano.account.representative()```
    - ```Nano.account.weight()```

### Block

Block methods either require a block hash as a single argument, or a stringified block:

    - ```Nano.block.account()```
    - ```Nano.block.count()```
    - ```Nano.block.chain()```
    - ```Nano.block.change()```
    - ```Nano.block.history()```
    - ```Nano.block.open()```
    - ```Nano.block.pending()```
    - ```Nano.block.publish()```
    - ```Nano.block.receive()```
    - ```Nano.block.send()```

### Blocks

This utility method allows a block hash or array of block hashes -

    - ```Nano.blocks.find()```

## Todos

* Use BigNumber, etc. to allow passing numbers
