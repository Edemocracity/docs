# Entity Resolver Contract

Voters may want to access an index of the available elections. The best way to organize them is by grouping them by the entity that created them.

In Vocdoni, entities publish [human readable metadata](/architecture/data-schemes/entity-metadata), but such metadata needs to be securely indexed on a Smart Contract.

The ENS Resolver Smart Contract allows entities to register and set the [Content URI](/architecture/protocol/data-origins?id=content-uri) that points to the actual [JSON metadata](/architecture/data-schemes/entity-metadata).

## Index

- [Text Record storage](#text-record-storage)
- [Text List Record storage](#text-list-record-storage)
- [Resolver keys](#resolver-keys)

---

## Entity Resolver

The **Entity Resolver** is the smart contract where [the metadata of each entity](/architecture/data-schemes/entity-metadata) is indexed. It leverages a standard ENS Resolver contract for its Text records, used as a key-value store.

The ENS domain of the contract instance is `entity-resolver.vocdoni.eth` on the ENS registry (soon replaced by `entities.vocdoni.eth` and `entities.dev.vocdoni.eth`).

The `entityId` is the unique identifier of each entity, being a hash of its Ethereum address:

```solidity
bytes32 entityId = keccak256 (entityAddress);
```

### Text Record storage

We make use of [EIP 634: Storage of Text records in ENS](https://eips.ethereum.org/EIPS/eip-634). It is a convenient way to store arbitrary data as a string following a key-value store model.

[Implementation](https://github.com/vocdoni/dvote-solidity/blob/master/contracts/profiles/TextResolver.sol)
  
### Supported Text Record keys

Entities may define the following Text Records:

| Key                                 | Example                                                       | Description                                                                                                           |
| ----------------------------------- | ------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------- |
| `vnd.vocdoni.meta`                  | 'ipfs://12345,https://server/json'                                    | [Content URI](/architecture/protocol/data-origins?id=content-uri) to fetch the Entity's JSON metadata. <br/>See [JSON schema](#meta). |
| `vnd.vocdoni.boot-nodes`            | 'ipfs://12345,https://server/gw.json'                                 | [Content URI](/architecture/protocol/data-origins?id=content-uri) to fetch a set of Gateways for the Entity. <br/>See [Gateway Boot Nodes](#gateway-boot-nodes) below. |
| `vnd.vocdoni.gateway-heartbeat`     | 'wss://host/path'                                      | [Messaging URI](/architecture/protocol/data-origins?id=messaging-uri) where the Gateways of the entity should report their health status. |

- Required
  - `vnd.vocdoni.meta`
- Optional
  - `vnd.vocdoni.boot-nodes`
  - `vnd.vocdoni.gateway-heartbeat`  (as long as the entity has no Gateways)

#### JSON Metadata

See [Entity Metadata](/architecture/data-schemes/entity-metadata?id=json-schema).

#### Gateway boot nodes

Client apps may not be able to join P2P networks by themselves, so Vocdoni makes use of Gateways to enable decentralized transactions over HTTP or Web Sockets. A gateway Boot Node is a service provided by the Entity, and it defines a list of active Gateways that a client can use.

By default, Vocdoni (as an Entity) provides its own set of Voting and Web3 Gateways. However, entities may want to use their own infrastructure. To this end, the Entity's ENS Text Record `vnd.vocdoni.boot-nodes` can be set to the Content URI of a [BootNodes JSON file](/architecture/services/bootnode) defining some of them.

### Coming next

See the [Process Contract](/architecture/smart-contracts/process) section.
