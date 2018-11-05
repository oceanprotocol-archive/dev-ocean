# Keeper Contract Design for Tethys

***DISCLAIMER: THIS IS A WORK IN PROGRESS***

This document provides technical details and describes the high-level design of keeper contract in ocean protocol.

## 1. Overview

### 1.1 Core Modules

From the top-level point of view, there are total 9 moduels as shown in the below:

<img src="img/refactoring/modules.jpg" width="1000" />

* **Ocean Token**: the native ERC20 token in Ocean network
* **Exchange**: Uniswap-like exchange built inside Ocean, where user can exchange between Ether and Ocean tokens.
* **Service Agreement**: every service is modeled as a *service agreement*. Please see [SLA.md](https://github.com/oceanprotocol/dev-ocean/blob/feature/SLA-specs/doc/sla.md) and [smart contract prototype](https://github.com/oceanprotocol/keeper-contracts/blob/develop/contracts/SLA/ServiceAgreement.sol) for details.
* **Tribe**: it controls the registry of membership for each service agreement template; defines TCR based registries for permission, which has whitelisted members who can provide or consume the service.
* **Curation**: users can curate a registry of high-quality service agreement templates through staking on corresponding bonding curves. It helps consumers discover high-quality assets/services. 
* **Governance**: it governs the available assets and services in Ocean network. For example, it whitelists new service agreement templates through TCR & Voting. 
* **Dispute Resolution**: resolves the disputes between consumer and provider through voting or TCR. 
* **Block Reward**: Ocean network will periodically release block reward (i.e., new minted ERC20 Ocean tokens) to reward the providers of data commons. Note provider of data commons must be randomly selected to fulfill the service requests so that they have the equal probability to receive block rewards.
* **Access Control**: it is the controller contract which grant access to consumers after depended conditions are fulfilled. The access token can be delivered in two approaches: secret store and OEP10. 
* **Payment**: it handles all payment processing, including lock payment, release payment and refund payment. 


### 1.2 Dependence

The core modules have dependence on each other, which can be illustrated as following. Clearly, the `service agreement` module is the fundamental building block, where many other modules depends on it.  

The same diagram can be found in [research slides](https://docs.google.com/presentation/d/1Z6Acq2LD3eHPD1SoH_bxHH-XaOKLWemgFiKgWICekys/edit#slide=id.g458a4e4ae3_0_7) page 73.

<img src="img/refactoring/dependence.jpg" width="1000" />

* **Service Condition**
	* **hash condition**: it provides the proof of hash to prove the secret was received. For example, the hash of votes in the commit stage are used to verify the voters indeed submit the same votes in the reveal stage.
	* **signature condition**: it verifies the signature to prove the ownership of private key for a public key. For example, service agreement needs the signature to verify the service request is signed by the consumer.
	* **threshold condition**: it verifies that the percentage of M-out-of-N is achieved such as multi-signature and public voting. For example, 50% of voters need to support the application in order to get it whitelisted. Similarly, M-out-of-N nodes in Secret Store must be available to decrypt the secret token.
	* **ethereum state condition**: it queries the state of ethereum ledger to prove certain condition. For example, block reward needs to query the status of service agreement and only reward providers of *fulfilled* service agreements.

* **Reward**:
	* **escrow account**: it holds the payment funds from consumer and only releases the payment to the provider when service agreement has been delivered.
	* **network reward**: it mints new Ocean tokens every block interval and rewards providers of data commons.


* **Dependence**:

Module Name  |  Condition Dependence |  Reward Dependence | Contract Dependence
--- | --- | ---| --- |
Voting | hash, signature, threshold, state conditions | | Ocean Token |
TCR | hash, signature, threshold, state conditions | | Voting |
Tribe | hash, signature, threshold, state conditions | | TCR |
Governance | hash, signature, threshold, state conditions | | TCR |
Dispute Resolution | hash, signature, threshold, state conditions | | Voting, TCR |
Block Reward | state condition | network reward | Ocean Token |
Secret Store | signature, threshold, state conditions | | |
Payment | state condition | escrow account | Ocean token |
Exchange | | | Ocean Token |
Access Control | signature, threshold, state conditions | | Secret Store |
Curation | | | Bonding Curves, Bonded Token |
Bonding Curves | | | Ocean Token, Bonded Token |
Bonded Token | | | |



## 2. Module Design

In this section, each module is designed with details (some materials comes from [Research Guidance](https://docs.google.com/presentation/d/1Z6Acq2LD3eHPD1SoH_bxHH-XaOKLWemgFiKgWICekys/edit#slide=id.g44065f7377_2_1894)) and key smart contract functions are provided.

### 2.1 Ocean Token 

#### 2.1.1 Definition

Ocean Token is the native and fungible tokens in Ocean network, which complies with the ERC20 Standard. 

Its total supply of Ocean Token is 1.4 billion.

45% of supply is reserved for block rewards, which will be minted according to schedule below:
<img src="img/refactoring/schedule.jpg" width="500" />


#### 2.1.2 Sample Code

The key functions in Ocean token is similar to below:

```Solidity
string public constant name = 'OceanToken';                      // Set the token name for display
string public constant symbol = 'OCN';                           // Set the token symbol for display

// SUPPLY
uint8 public constant decimals = 18;                             // Set the number of decimals for display
uint256 public constant TOTAL_SUPPLY = 1400000000 * 10 ** 18;    // OceanToken total supply

// transfer tokens 
function transfer(address _to, uint256 _value) public returns (bool);

// mint new Ocean tokens and send tokens to block reward contract 
function mintTokens() public returns (bool success);
```

#### 2.1.3 Task & Issue

- [ ] mint new tokens as block rewards according to the releasing schedule;
- [ ] transfer minted new tokens to block reward pool.

----

### 2.2 Block Reward

#### 2.2.1 Definition

Block rewards are newly minted Ocean tokens which reward providers of data commons. 

<img src="img/refactoring/blockreward.jpg" width="1000" />

**Key Points**:

* Block reward Ocean tokens will be minted and deposited into a reward pool;
* In the same block interval, Ocean network randomly select a provider to serve the service agreement of data commons. Therefore, all providers have the equal probability to receive the block rewards.
* When the service agreement is fulfilled and verified, provider receive a `lottery ticket` to win the partial block rewards;
* Stakeholders of data commons in the same block interval receive a `lottery ticket` to win the partial block rewards.
* In the end of current block interval, Ocean network randomly choose a winner from the providers and a winner from the stakeholders who hold `lottery tickets`.
* This chosen provider and stakeholder share all tokens in the reward pool (the reward percentage is pre-defined, such as provider takes 80% block rewards, while stakeholder takes 20%);
* The procedure will repeat for each block interval; if there is no winner, Ocean tokens in the reward pool will accumulate over time until a winner claims the reward.

<img src="img/refactoring/rewardLogic.jpg" width="1000" />

The process for a service prodiver to receive block rewards in one block interval is below:

<img src="img/refactoring/distributeBR.jpg" width="800" />

#### 2.2.2 Sample Code

The sample smart contract looks like this:

```Solidity
// providers of data commons in this block interval
address[] candidates;

// number of reward tokens in this block interval
uint256 rewardPool;

// randomly pick a provider to receive block reward
function sendBlockReward() public returns (bool success){
	...
	// Ocean token contract mints new tokens
	OceanTokenContract.mintTokens();
	
	// randomly pick a provider from the candidates (need a secure Random Number Generator!!!)
	uint256 random = keccak256(abi.encodePacked(...)) % n;
	
	// send tokens in rewardPool to a randomly chosen provider
	transfer(candidates[random], rewardPool);
	...
};
```

#### 2.2.3 Task & Issue

- [ ] retrieve proofs of fulfilled service agreements for data commons;
- [ ] add providers of these proofs to the winner candidate list;
- [ ] retrieve list of stakeholders for data commons;
- [ ] random number generator to have uniform-distributed random numbers;
- [ ] detect the end of current block interval;
- [ ] trigger the minting of new block rewards in Token contract;
- [ ] choose a random provider and a random stakeholder;
- [ ] split the current reward pool into token rewards for chosen provider and stakeholder.

----

### 2.3 Service Agreement

#### 2.3.1 Definition

Please see [SLA.md](https://github.com/oceanprotocol/dev-ocean/blob/feature/SLA-specs/doc/sla.md) and [smart contract prototype](https://github.com/oceanprotocol/keeper-contracts/blob/develop/contracts/SLA/ServiceAgreement.sol) for details. 

The structre of a Serivce Agreement looks like below:

<img src="img/refactoring/SAStructure.jpg" width="700" />

The key point is to split up the conditions from reward payment as:
<img src="img/refactoring/SAOverview.jpg" width="1000" />


**Some changes to be added:**


1. **`Time Lock` and `Time Out`:** Each service condition has `time-lock` and `time-out`. It can only be fulfilled within the time window defined by the starting time point (`time-lock`) and the ending time point (`time-out`).
<img src="img/refactoring/lifecycle.jpg" width="800" />

2. **support Tri-State: Unknown, True, False**
<img src="img/refactoring/triState.jpg" width="800" />

3. **New service agreement template must go through governance and TCR to be whitelisted**. 
<img src="img/refactoring/whitelistSAT.jpg" width="800" />

4. **Add current provider of data commons into block reward candidates if service agreement is fulfilled and verified.**

#### 2.3.2 Sample Code

The sample smart contract looks like below:

```Solidity
// setup new whitelisted SA template and add it into Tribe and Curation contract
function setupAgreementTemplate(address[] contracts, bytes4[] fingerprints, uint256[] dependenciesBits, bytes32 service) public returns (bool){
	...
	// service agreement template must be whitelisted at this time
	require(service.isWhitelisted == true);
	...
	// add SA template into Tribe contract and Curation contract
	OceanTribe.addServiceTemplate(bytes32 templateId);
	OceanCuration.addServiceTemplate(bytes32 templateId);
	...
};

// instantiate template to create a service agreement (randomly choose a provider of data commons)
function executeAgreement(bytes32 templateId, bytes signature, address consumer, bytes32[] valueHash, uint256[] timeoutValues) public {
	...
	// check consumer has permission to consume the service
	if (dataset is public and free to access || consumer in Tribe registry and has permission) 
	...
};

// fulfill the service agreement (after all conditions are fulfilled)
function fulfillAgreement(bytes32 serviceId) public noPendingFulfillments(serviceId) returns(bool){
	...
	// detect new block interval and distribute tokens in current block reward pool
	if( find a new block interval) {
		// distribute block reward tokens 
		BlockReward. sendBlockReward();  
		// reset the candidate list	
		BlockReward.clearCandidateList()
	}
	...
	// add current provider of data commons into candidate list of block reward 
	BlockReward.addCandidate(address provider);
	...
};

// fulfill specific service condition (common interface)
function fulfillCondition(bytes32 serviceId, bytes4 fingerprint, bytes32 valueHash) public returns(bool);

// calculate the amount of payment released to provider and refunded to consumer
function calculatePayment(bytes32 serviceId) public returns(bool);
```

#### 2.3.3 Task & Issue

- [ ] add time-lock and time-out to each service condition and check its validity when needed;
- [ ] add support of UNKNOWN status;
- [ ] when create new SA template, the template must go through TCR to get whitelisted;
- [ ] add new SA template into Tribe and Curation registry;
- [ ] add permission checking with Tribe registry;
- [ ] when SA is fulfilled, check whether it is needed to distribute block reward. If not, add provider of data commons to the winner candidate list.
- [ ] In the end of block interval, trigger the distribution of block rewards.

----

### 2.4 Payment

#### 2.4.1 Definition

Payment module handls all payment processing: lock payment, release payment and refund payment. It provides query functions for external function to check the payment status of a specific service agreement.


#### 2.4.2 Sample Code

```Solidity
// lock consumer payment
function lockPayment(bytes32 serviceId, bytes32 assetId, uint256 price) public returns (bool);

// release consumer payment to provider
function releasePayment(bytes32 serviceId, bytes32 assetId, uint256 price) public returns (bool);

// refund payment to consumer
function refundPayment(bytes32 serviceId, bytes32 assetId, uint256 price) public returns (bool);
```

#### 2.4.3 Task & Issue

- [ ] add function to distribute block rewards with pre-defined percentage parameters;
- [ ] add function to verify the status of payments.


----

### 2.5 Access Control

#### 2.5.1 Definition

This module grant consumer with the access to assets. Consumer has two options to receive the access token:

* **Secret Store**: Secret Store is used to encrypt/decrypt a URL, which can only be decrypted by the authorized consumer. The secret store integrates the Service Agreements Smart contracts to authorize the consumer.

	* [Javascript Secret Store Driver](https://github.com/oceanprotocol/secret-store-client-java).
	* [Python Secret Store Driver](https://github.com/oceanprotocol/secret-store-client-py)

* **On-Chain authorization**: provider delivers the encrypted access token through on-chain smart contract using service agreement. Details in [OEP-11 On-Chain Access Control using Service Agreements](https://github.com/oceanprotocol/OEPs/tree/feature/OEP-11_acl_sa/11)

#### 2.5.2 Sample Code

```Solidity
// grant access to the consumer in given service agreement
function grantAccess(bytes32 serviceId, bytes32 assetId) public returns (bool);
```

#### 2.5.3 Task & Issue

- [ ] add support of data commons. 
- [ ] grant access according to Tribe registry.

----

### 2.6 Tribe

#### 2.6.1 Definition

Each service agreement template has its corresponding `Tribe` including:

* *open registry* that enables membership for this SA template;
* *provider registry* includes members who can fulfill the instantiated service agreement;
* *consumer registry* includes members who are eligible to consume the instantiated service agreement;

<img src="img/refactoring/tribe.jpg" width="600" />

**How to add member into Provider registry?**

* owner of service agreement template is a provider of the instantiated service agreement by default;
* other members in the tribe shall be whitelisted through TCR in order to fulfill the service agreement.

**How to add member into Consumer registry?**

* pre-vetted users by the owner of datasets;
* consumers who makes payment for the service agreement;
* all members have access to data commons.

**How to choose Provider for SA?**:

* *data commons*: Ocean network randomly choose a provider to fulfill the service agreement request of data commons;
* *priced data*: Consumer chooses the provider to fulfill his service agreement request.

#### 2.6.2 Sample Code

```Solidity
struct Tribe{
   // open registry of membership
	mapping(address => bool) private members;
	
	// TCR based registry for provider permission: who can provide service
	mapping(address => bool) private providers;
	
	 // TCR based registry for consumer permission: who can consume service
	mapping(address => bool) private consumers;
}

// each SA template has its corresponding Tribe struct 
mapping(bytes32 => Tribe) private tribes;

// add new member to the tribe (similarly, there is function to remove a member)
function addMember(bytes32 template_Id, address member) public returns (bool);

// add new provider to fulfill the SA (similarly, there is function to remove a provider)
function addProvider(bytes32 template_Id, address provider) public returns (bool);

// add new consumer to consume the SA (similarly, there is function to remove a consumer)
function addConsumer(bytes32 template_Id, address consumer) public returns (bool);
```

#### 2.6.3 Task & Issue

- [ ] add data structures of permission registries for membership, provider and consumer;
- [ ] user needs to go through TCR to be added into provider registry;
- [ ] user needs to make payment or pre-vetted to be added into consumer registry;
- [ ] members in the tribe have access to data commons; there is no need to have consumer registry;
- [ ] add functions to remove user from provider registry or consumer registry;

----

### 2.7 Curation

#### 2.7.1 Definition

Curation is required in Ocean to generate a ranked list of SA sorted from high quality to low. In the decentralized settings, curation could be done with staking mechanism & bonding curves.

A bonding curves is a fixed curve that bonds reserved token (i.e., Ether or Ocean token) with a local ERC20 boned token for an associated SA:

<img src="img/refactoring/BC.jpg" width="1000" />

The bonding curves determines the exchange ratio with an analytical formula, which models the price of bonded token as a function of its total supply. Usually price goes up when total supply increases, which indicates strong demand of bonded tokens.

**(1) Curation for SA template**

* Each service agreement template has its own fixed bonding curve along with ERC20 bonded tokens;
* Whenever the ERC20 bonded token is minted or burnt, the fixed bonding curve is used to calculate the price of bonded tokens:
	* calculates how many bonded tokens should be minted for a given amount of Ocean tokens;
	* calculates how many Ocean tokens should be returned when a given amount of bonded tokens are burnt.

**(2) The design structure**:

<img src="img/refactoring/BCstructure.jpg" width="700" />

**(3) Staking and mint new bonded token**:
<img src="img/refactoring/BCstaking.jpg" width="1100" />

**(4) Unstaking and burn bonded token**:
<img src="img/refactoring/BCunstaking.jpg" width="1100" />


#### 2.7.2 Sample Code

The example smart contract functions for curation are below:


```Solidity
struct Curation {
   // fixed bonding curve
	BondingCurve private bondingCurve;
	// bonded token	
	BondedToken private bondedToken;
}

// each SA template has its own Bonding Curve and Bonded Token
mapping(bytes32 => Curation) private curations;

// create new curation market for a new service agreement template
function createCurationMarket(bytes32 template_Id) public returns (bool);

// buy bonded token from a specific bonding curve
function mintBondedToken(bytes32 template_Id, uint256 numOceanToken) public returns (uint256 numBondedToken);

// sell bonded token into a specific bonding curve
function burnBondedToken(bytes32 template_Id, uint256 numBondedToken) public returns (uint256 numOceanToken);
```

The Bonded tokens is fungible ERC20 token as following:

```Solidity
// transfer tokens 
function transfer(address _to, uint256 _value) public returns (bool);

// mint new tokens for buyDrops transaction
function mintTokens() public returns (bool success);

// burn tokens for sellDrops transaction
function burnTokens() public returns (bool success);
```

#### 2.7.3 Task & Issue

- [ ] add bonded token contract to support mint bonded token and burn bonded token;
- [ ] deploy a new bonded token contract for each SA template so users can stake;
- [ ] add curation contract to manage the curation markets for SA templates;
- [ ] users can stake reserved token for bonded token and sell bonded tokens back with curation contract;
- [ ] curation contract use pre-defined bonding curves contract to determine the exchange rate.

---

### 2.8 Dispute Resolution

#### 2.8.1 Definition

Consumer can raise dispute before *TIME-OUT* and *PAYMENT-RELEASED* in the service agreement. 

If dispute exists, the consumer payment is locked and cannot be released before the dispute is resolved.

To resolve the dispute, there are two approaches:

* **Voting based resolution**: it enables expert verification (or arbitration) with permissioned voting on the disputes. 
* **TCR based resolution**: it enables community opinion since all community members can vote on the disputes, which will be resolved based on TCR results.

#### 2.8.2 Sample Code

```Solidity
struct Dispute {
   // TCR based resolution for community opinion
	uint256 challenge;
	
	// voting based resolution for human verification
	PLCRVoting  voting; 
	
	// status
	bool  status;
}

// each SA instance has its own Dispute struct
mapping(bytes32 => Dispute) private disputes;

// consumer raise dispute for a given service agreement instance before timeout & release payment
function addDispute(bytes32 serviceId) public returns (bool){
	...
	 // set “dispute=true” flag in service agreement to prevent the release of payment
	ServiceAgreement.disputeExist(bytes32 serviceId);
	...
};

// resolve dispute with two approaches
function resolveDispute(bytes32 serviceId) public returns (bool);
```

#### 2.8.3 Task & Issue

- [ ] add dispute resolution contract to manage all disputes;
- [ ] enable consumers to raise dispute for a specific SA;
- [ ] disable processing of payment if dispute exists;
- [ ] resolve dispute with voting or TCR;
- [ ] update the SA status according to dispute resolution result.

---

### 2.9 TCR (Token Curated Registry)

#### 2.9.1 Definition

**1. Concept**

TCR is a common module that can be used by many different components. Our TCR is derived from Mike Goldin's prototype of TCR. 

<img src="img/refactoring/tcr.jpg" width="800" />

**2. Event Driven Design**

TCR may be used for different registries (such as SA template, dispute resolution and tribe registry), therefore, the whitelisting result needs to trigger corresponding actions depends on the TCR voting results.

Similar to SA event triggering design, the various actions are triggered by event messages:

* Applications are submitted to TCR for voting;
* If application is whitelisted, TCR contract emits an event message including all critical information;
* The external controller contract is listening to the events from TCR for a specific application;
* When received the message, the controller contract can trigger the action such as add new SA template into the registry. 

<img src="img/refactoring/tcrAction.jpg" width="500" />


#### 2.9.2 Sample Code

The sample smart contract is below:

```Solidity
// each application has Listing struct
mapping(bytes32 => Listing) private listings;

//  each challenge has Challenge struct that keeps track of votes and result
mapping(uint => Challenge) private challenges;

// submit application
function apply(bytes32 _listingHash, uint _amount, uint _type, string _data) public;

// challenge an application
function challenge(bytes32 _listingHash, string _data) external returns (uint challengeID);

// resolve an challenge
function resolveChallenge(bytes32 _listingHash) private;

// update registry
function updateRegistry(bytes32 listingHash) public {
	...
	// TODO: emit an event to add new item into a specific registry
	emit isWhitelisted(...);
	...
}
```

#### 2.9.3 Task & Issue

- [ ] add functions to emit event message when a challenge is resolved by voting;
- [ ] implement an external event message listener which keeps listening to events;
- [ ] the external listner triggers event to update the corresponding registry.

---

### 2.10 Voting 

#### 2.10.1 Definition

Voting module is based on PLCRVoting implementation. 

One change to be added is to enable **permissioned voting**, where only voters on the whitelist/registry can participate in the voting.

#### 2.10.2 Sample Code


```Solidity
// voting struct
struct Voting {
	// whether the voting is restricted to a group of voters in the mapping below
	bool restrictedVoting;
	
	// define the list of voters for specific voting
	mapping(address => bool) voters;
}

// each voting has its own Voting struct
mapping(bytes32 => Voting ) private votings;

// grant access to the consumer in given service agreement
function commitVote(bytes32 id) public returns (bool){
	...
	 // check voter’s permission if needed
	if (votings[id]. restrictedVoting == true ){
		require(votings[id]. voters[msg.sender] == true);
	}
	...
	
};
```

#### 2.10.3 Task & Issue

- [ ] add voter list for each voting;
- [ ] if enabled, voter must be on the list in order to participate the voting;
- [ ] the commitVote function adds permission check for voters if needed.

## 3. Integration of Ocean Modules

These different modules can be put together in the below diagram. 

It illustrates the interaction between contracts. 

<img src="img/refactoring/refactoring.jpg" width="1000" />
















