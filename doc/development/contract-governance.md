# Contract Governance and Deployment

## Governance

Since smart contracts cannot be edited (only redeployed with different address) once they are deployed on-chain we need 
to take care about still beeing upgradeable in case there is a bug in the contracts. To achieve this goal Businesslogic 
and Data needs to be in separated contracts. These contracts must be linked together with their address. It is more 
likely that there is a bug in the businesslogic contracts then in the data contract. This will bring us in a position 
where we need to replace the businesslogic, keep the data and relink both.

There are two concepts of storing data outside of a contract. Structured and Unstructured.

### Structured Storage

A Structured storage is a contract holding your relocated data. 

```solidity
contract RegistryStorage {

    struct Listing {
        uint applicationExpiry; // Expiration date of apply stage
        bool whitelisted;       // Indicates registry status
        address owner;          // Owner of Listing
        uint unstakedDeposit;   // Number of tokens in the listing not locked in a challenge
        uint challengeID;       // Corresponds to a PollID in PLCRVoting
    }

    struct Challenge {
        uint rewardPool;        // (remaining) Pool of tokens to be distributed to winning voters
        address challenger;     // Owner of Challenge
        bool resolved;          // Indication of if challenge is resolved
        uint stake;             // Number of tokens at stake for either party during challenge
        uint totalTokens;       // (remaining) Number of tokens used in voting by the winning side
        mapping(address => bool) tokenClaims; // Indicates whether a voter has claimed a reward yet
    }
    
    // Maps challengeIDs to associated challenge data
    mapping(uint => Challenge) public challenges;
    
    // Maps listingHashes to associated listingHash data
    mapping(bytes32 => Listing) public listings;
}
```

### Unstructured Storage

This storage consist of just key value pairs that are stored in the contract. One example is [Eternal Storage](https://medium.com/rocket-pool/upgradable-solidity-contract-design-54789205276d).
There is also an [EIP ERC930](https://github.com/ethereum/EIPs/issues/930) making Eternal Storage a standard. 

## Deployment

We use [truffle](https://github.com/trufflesuite/truffle) to test and deploy solidity smart contracts.

We use [ganache](https://github.com/trufflesuite/ganache-cli) as local backend for truffle testing and development.
