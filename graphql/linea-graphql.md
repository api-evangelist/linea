# Linea GraphQL API

## Description

The Linea GraphQL API is provided by the Blockscout-based block explorer at `explorer.linea.build`. It exposes blockchain data for Linea Mainnet (chain ID 59144) through a GraphQL interface, enabling queries for addresses, blocks, transactions, internal transactions, token transfers, and smart contract information.

The API is backed by the Blockscout Elixir backend and supports both queries and subscriptions (real-time token transfer events via WebSocket).

## Endpoint

**HTTP (queries):**
```
https://api-explorer.linea.build/api/v1/graphql
```

**WebSocket (subscriptions):**
```
wss://api-explorer.linea.build/socket/websocket
```

**GraphiQL UI:**
```
https://explorer.linea.build/graphiql
```

## Authentication

No API key required. The endpoint is publicly accessible. Standard Blockscout rate limits apply.

## Supported Operations

### Queries

| Query | Description |
|-------|-------------|
| `address(hash: AddressHash!)` | Fetch a single address with balance, transaction count, and token transfers |
| `addresses(hashes: [AddressHash]!)` | Fetch multiple addresses by hash |
| `block(number: Int!)` | Fetch a block by number with gas, miner, hash, and timestamp |
| `node(id: ID!)` | Fetch any node by global ID |
| `tokenTransfers(tokenContractAddressHash: AddressHash!, ...)` | Paginated token transfers for an ERC-20/721/1155 contract |
| `tokenTransferTxs(addressHash: AddressHash, ...)` | Paginated transfer transactions for an address |
| `transaction(hash: FullHash!)` | Fetch a transaction by hash with full details |

### Subscriptions

| Subscription | Description |
|-------------|-------------|
| `tokenTransfers(tokenContractAddressHash: AddressHash!)` | Real-time stream of token transfer events for a contract |

## Pagination

Queries returning collections use cursor-based pagination (Relay-style connections) with `first`, `last`, `after`, `before`, and `count` arguments. Each connection type has `edges { cursor node { ... } }` and `pageInfo { hasNextPage hasPreviousPage startCursor endCursor }`.

## Example Query

```graphql
{
  transaction(hash: "0xabc123...") {
    hash
    blockNumber
    fromAddressHash
    toAddressHash
    value
    gasUsed
    status
    block {
      timestamp
      number
    }
  }
}
```

```graphql
{
  tokenTransfers(
    tokenContractAddressHash: "0x176211869cA2b568f2A7D4EE941E073a821EE1ff"
    first: 10
  ) {
    edges {
      node {
        fromAddressHash
        toAddressHash
        amount
        transactionHash
      }
    }
    pageInfo {
      hasNextPage
      endCursor
    }
  }
}
```

## Documentation

- GraphiQL Explorer: https://explorer.linea.build/graphiql
- Blockscout API docs: https://docs.blockscout.com/devs/apis/graphql
- Linea Explorer: https://explorer.linea.build
- Schema: linea-schema.graphql
