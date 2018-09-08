# The Ballzcoin White Paper


## Contents

- [Introduction and Goals](https://github.com/ballzcoin/whitepaper/blob/master/README.md#introduction-and-goals)<br />
- [Technical Features and Usage](https://github.com/ballzcoin/whitepaper/blob/master/README.md#technical-features-and-usage)<br />
  - [Proof of Work](https://github.com/ballzcoin/whitepaper/blob/master/README.md#proof-of-work)<br />
  - [DApps](https://github.com/ballzcoin/whitepaper/blob/master/README.md#dapps)<br />
  - [Payment Mechanism](https://github.com/ballzcoin/whitepaper/blob/master/README.md#payment-mechanism)<br />
  - [Rapid Difficulty Adjustment](https://github.com/ballzcoin/whitepaper/blob/master/README.md#rapid-difficulty-adjustment)<br />
  - [Network Segregation](https://github.com/ballzcoin/whitepaper/blob/master/README.md#network-segregation)<br />
  - [General](https://github.com/ballzcoin/whitepaper/blob/master/README.md#general)<br />
  - [Source Code](https://github.com/ballzcoin/whitepaper/blob/master/README.md#source-code)<br />
- [Client and Blockchain Specification](https://github.com/ballzcoin/whitepaper/blob/master/README.md#client-and-blockchain-specification)<br />
- [Currency](https://github.com/ballzcoin/whitepaper/blob/master/README.md#currency)<br />
  - [Issuance](https://github.com/ballzcoin/whitepaper/blob/master/README.md#issuance)<br />
  - [Total Supply](https://github.com/ballzcoin/whitepaper/blob/master/README.md#total-supply)<br />
  - [Charity Reward](https://github.com/ballzcoin/whitepaper/blob/master/README.md#charity-reward)<br />
  - [Premine](https://github.com/ballzcoin/whitepaper/blob/master/README.md#premine)<br />
- [Roadmap](https://github.com/ballzcoin/whitepaper/blob/master/README.md#roadmap)<br />
- [Version History](https://github.com/ballzcoin/whitepaper/blob/master/README.md#version-history)<br />


## Introduction and Goals

Ballzcoin is a light-hearted cryptocurrency project based on the Go implementation of Ethereum. 

The primary goals of Ballzcoin are as follows:
- To encourage talking about our balls, by supporting men’s health charities via the block reward. Ballzcoin is an equal opportunities cryptocurrency, and so women’s and children’s health charities will also be supported.
- Provide an alternative decentralized Proof of Work based blockchain to run EVM smart contracts and DApps, without a future transition to Proof of Stake.
- Encourage and incentivize the development of DApps to run on the Ballzcoin network, with an emphasis on fun Dapps.  
- Implement a difficulty adjustment algorithm that responds rapidly to changes in mining hash rate, to maintain the target average block time under changing network conditions.
- Keep the project fun and open source. 
- All goals and expectations will be kept realistic.


## Technical Features and Usage


### Proof of Work

Ballzcoin is a Proof of Work cryptocurrency. Ballzcoin will not transition to Proof of Stake in the future, and will always rely on miners to secure the network and process transactions. 

This decision is based on the underlying philosophy that there must be a mechanism to create intrinsic value in the first place. Mining the currency requires time, electricity, and capital cost of equipment. The values of many cryptocurrencies (including Bitcoin) are closely related to the cost in electricity of mining a new coin. This financial investment is not required with Proof of Stake, which raises the question of whether a currency based on Proof of Stake from the outset has any intrinsic value. 



### DApps

The costs associated with mining will generate some intrinsic value, however ultimately the cryptocurrency must have an underlying purpose to maintain / increase in value. In the case of Ballzcoin, the currency will be used to power DApps on the blockchain. 

One of the primary goals of the project is to provide a network that allows developers to run DApps. There is a cost associated with running DApps (gas), which can be expensive on the Ethereum network due to the high value of Ether. Many DApps, in particular some games, lose some of their appeal with high transaction fees. Ballzcoin is likely to have a significantly lower value than Ethereum in the long term, therefore providing a lower cost alternative blockchain for DApp developers. 


### Payment Mechanism

Whilst development of Ballzcoin as a widespread means of paying for goods and services is not currently in the roadmap, it is conceivable that apps / platforms will be developed by others in the wider cryptocurrency space in the not too distant future, that will allow payment for goods and services to be made with all cryptocurrencies, including Ballzcoin.  


### Rapid Difficulty Adjustment

Fundamentally, mining a block must be as fair as possible to all participants large or small, to attract miners to the currency in the first place and retain their ongoing support. For this reason, the difficulty adjustment algorithm in Ballzcoin has been rewritten from scratch with this purpose in mind. 

#### Background

A statistical analysis of the Ethereum difficulty adjustment algorithm ([Byzantium rules](https://github.com/ethereum/go-ethereum/blob/f34f361ca6635690f6dd81d6f3bddfff498e9fd6/consensus/ethash/consensus.go#L320)) on a test network, over a period of 100,000 blocks at a constant mining hash rate indicates that the average block time is 12.4s. The mining of a block is a probabilistic event, resulting in individual block times ranging from 1 second to 157 seconds, observed over the 100,000 block period.   

If there is a step change in hash rate, there will be a corresponding inverse step change in average block time. For example, further analysis indicates that if the hash rate suddenly increases by a factor of 6, the average block time reduces to approximately 2.2s. It takes approximately 10,000 blocks for the difficulty adjustment algorithm to bring the block time back to the average of 12.4 seconds.

The main Ethereum blockchain is well established, and has a very high mining hash rate (>200Th/s at the time of writing). In general, fluctuations in the mining hash rate are small and gradual, resulting in the standard difficulty adjustment algorithm being well suited to the task of keeping the average block time on target.

It is obvious from the statistical analysis above that the standard algorithm has some disadvantages when used by a cryptocurrency with a much lower overall mining hash rate. Smaller cryptocurrencies are far more likely to experience large step changes in mining hash rate as miners start and stop mining the coin based on daily profitability. The most obvious negative effects of a slow response to changes in hash rate are as follows:

- Very low average block times resulting in a large number of 'stale' blocks.
- The risk of chain reorganizations due to average network propagation times being slower than average block times.  
- Very large miners are incentivized to mine for a brief period when the difficulty is low, and stop mining when the difficulty has caught up to the hash rate. This results in longer block times while the difficulty adjusts downwards, and corresponding lower rate of block rewards for the smaller miners who continue to mine.
- The issuance rate of the currency will be skewed from initial projections.

These effects are likely to be exacerbated during the days immediately after the launch of the cryptocurrency, as miners are incentivized to mine the coin due to the large discrepancy between difficulty and network hash rate. Inevitably, the starting difficulty will be set far lower than the anticipated network hash rate (the alternative scenario being the risk of a stuck block chain that nobody wants to mine until the difficulty has readjusted downwards).

#### Algorithm Description

The Ballzcoin difficulty adjustment algorithm has been written from scratch to address these issues, with the aim of rapidly adjusting the difficulty after large changes in network hash rate to maintain the target average block time.

The algorithm comprises of a series of conditional statements which adjust the difficulty of the next block based on the time of the previous block. The magnitude and direction of the adjustment is proportional to the block time, with the adjustment heavily weighted for very low and very high block times. The adjustment values and block time adjustment thresholds have been determined by statistical analysis of the spread of block times during steady and changing network hash rates, and iteratively refined during prototype testing of the algorithm on a test network.
 
A decision was made during development of the algorithm that only time stamps from the last two blocks would be used as a basis for adjustment of the difficulty, rather than taking a moving average of the last n block times. This approach optimizes the response time, and simplifies the algorithm by disregarding any issues that could arise when revalidating blocks after a chain reorganisation or dealing with uncle blocks.

#### Algorithm Performance

At the time of writing Version 1.0 of the white paper, the performance of the algorithm has been evaluated on a test network. Results are documented below, and will be updated in a future version of the white paper after the main network has been running for some time, which will be the true test of the algorithm performance.

Average block time: 14.5 seconds measured over 150,000 blocks at varying hash rate (this figure is rounded up to 15 for simplicity under the Blockchain Specification section below).

Block time variance: Minimum 1 second, maximum 173 seconds, at constant hash rate measured over 100,000 blocks. 

Typical number of blocks required to adjust difficulty to bring block time back on target:
- 4 fold change in hash rate: 125 blocks
- 250 fold change in hash rate: 250 blocks

Average variance in difficulty: +50%/-30% at a constant hash rate. As anticipated, this is significantly higher than Ethereum, which has a measured variance of approximately +7%/-5% using the Byzantium algorithm. The higher variance is a byproduct of rapid response time.

#### Algorithm Security Considerations

One of the potential risks of rapid difficulty adjustments is incentivizing miners to manipulate the block timestamp under certain conditions. Consider the scenario where a miner with >51% of the network hashing power artificially increments the block timestamp for every mined block, resulting in an ever decreasing difficulty over time. Since the true block time is less than the time reported, this allows the miner to eventually mine multiple blocks every second, pocketing a significant block reward. This type of attack [has in fact occurred](https://www.coindesk.com/verges-blockchain-attacks-are-worth-a-sober-second-look/) on a cryptocurrency with a rapid difficulty adjustment algorithm.

This type of attack is unlikely on Ballzcoin, as there are two fundamental checks built into the underlying Ethereum protocol which when combined, make this type of attack uneconomical.
 
The first check relates to the validity of the time stamp. When a block is successfully mined, the miner time stamps the block. For a block to be valid, the time stamp of the current block must be more recent than the parent block. When time stamping each block, the Ethereum protocol uses a centralized time reference taken from the NTP Pool Project (pool.ntp.org), which is a network of >3000 servers.

When a newly mined block is propagated to other peers in the network for validation, each peer checks that the block has not been mined more than [15 seconds into the future](https://github.com/ethereum/go-ethereum/blob/f34f361ca6635690f6dd81d6f3bddfff498e9fd6/consensus/ethash/consensus.go#L42). If this is the case, the block will be rejected. The miner of this block will also be dropped by the other peers on the network for being out of synch.

The rogue miner can continue to mine on a forked chain while disconnected from other peers, generating block rewards on the forked chain with minimum difficulty and fast block times. Each faked time stamp pushes the forked chain further into the future (to maintain a low difficulty, a block mined in a fraction of a second must still have a fake time stamp indicating that the block has taken 13 seconds or more). The miner can then stop and wait for the main chain to catch up to the future time on the forked chain, and attempt to re-join the network once synchronized. At this point, potentially, the honest peers could be presented with two chains, prompting a chain reorganization in favour of the forked chain.  

The second check built into the protocol is the ['heaviest chain' rule](https://github.com/ethereum/go-ethereum/blob/525116dbff916825463931361f75e75e955c12e2/core/blockchain.go#L863). When faced with a forked chain, the clients will select the chain which has had the most computation done on it, i.e. the chain with the highest cumulative difficulty. The forked chain would have been mined at a very low difficulty per block, whereas the main chain would continue to grow with a high difficulty per block. Even with >51% of the network hashing power, prior to synchronization the attacker would need to expend considerable time and computational power mining blocks with a difficulty more than the difficulty of the main network to ensure a heavier block chain. The effort required would likely cancel out the financial gains made by manipulating the block times.  

In any case, an attacker with >51% of the hashing power would be far more likely to carry out double spending attacks which would be more profitable and easier to implement than a time spoofing attack.


### Network Segregation

A minor change has been made to the p2p discovery code in Ballzcoin, to ensure that pings from non-Ballzcoin clients, in particular the main Ethereum network, are ignored. This prevents the addresses of non-Ballzcoin clients from being stored in the node database, which can cause delays in finding valid Ballzcoin nodes.


### General

As Ballzcoin is based on the Go Ethereum client, all other features are the same as [Ethereum](https://github.com/ethereum/wiki/wiki/White-Paper), including the execution of smart contracts written in Solidity or Serpent.


### Source Code

All source code for the project will be open and freely available on Github (https://github.com/ballzcoin) as soon as the client binaries are available for download on the day of release.


## Client and Blockchain Specification

The Ballzcoin client is based on the Go implementation of Ethereum (Geth), with some minor changes as detailed under the Technical Features heading above.

The key parameters are:
- Client binary name: ballzcoin (Linux), ballzcoin.exe (Windows)
- Algorithm: Ethash
- Average block time: 15 seconds
- RPC port: 8641
- WS port: 8642
- UDP port: 31805
- Chain ID: 2025418852
- Block 0 hash: to be updated on release
- Initial release version number: 1.8.12

Version control will follow the numbering format of the official Geth client releases, however Ballzcoin will not necessarily be updated every time Geth is updated. Updates will be made periodically, in particular when any major bugs are identified and fixed, or significant improvements are made.

In the event of an update to Ballzcoin that does not coincide with an update to Geth, a fourth number will be appended to the version (e.g. 1.8.12 becomes 1.8.12.1).


## Currency

The currency of the network is Ballzcoin, or BALLZ. One Ballzcoin is divisible into 10<sup>18</sup> sub-units called 'wei' in the underlying protocol.


### Issuance

The majority of the currency supply will be minted as a block reward to miners.

The block reward payable to miners reduces with time in accordance with the following schedule:
- Year 1: 5 coins per block
- Year 2: 4 coins per block
- Year 3: 3 coins per block
- Year 4: 2 coins per block
- Year 5: 1 coin per block
- Year 6 onwards: 0.5 coins per block

This does not include block rewards paid to uncles, and is based on an average block time of 15s.

The timing for the annual decrease in block reward is approximate, and triggered by block number in accordance with the following schedule:
- Start of year 2: block number 2,100,000
- Start of year 3: block number 4,200,000
- Start of year 4: block number 6,300,000
- Start of year 5: block number 8,400,000
- Start of year 6: block number 10,500,000


### Total Supply

The total coin supply after 5 years, excluding rewards to uncles, will be approximately 35 million Ballzcoin. From year 6 onwards, the annual supply will increase by approximately one million coins per year. There is no hard limit, as miners must always be incentivized to secure the network.


### Charity Reward

A key feature of Ballzcoin is that there is an additional block reward given to charity, separate to the miner reward. This is equivalent to 5% of the miner reward, and is divided up between 10 charity accounts. Each charity account will be held by a different charity, completely independent from the Ballzcoin development team.

The charity reward will be activated after block number 1,050,000 (approximately 6 months from the start), after which time the charity accounts will begin accumulating rewards. The intention is to distribute all 10 charity accounts by this time. If any accounts have not been distributed by the time the charity reward is activated, these accounts will be distributed between the charities who already have accounts. No charity accounts will be retained by the Ballzcoin team after 6 months.  


### Premine

A premine of 1,500,000 Ballzcoin (equivalent to ~4% of total coin supply after 5 years) has been encoded into the genesis block. This will be used by the development team for purposes including promotion, development, server / web hosting costs, bounties, and exchange listings. The premine will also be used to incentivize the development and deployment of DApps on the Ballzcoin network by third parties. The emphasis will be on fun DApps, especially games, however all DApps are welcome. Some of the premine will also be retained as a developer reward, if Ballzcoin performs as intended and generates value in the long term. 


## Roadmap

Initial release:
- All source code available on Github.
- Linux and Windows Ballzcoin clients released (including command line wallet).
- Linux and Windows GUI wallets released.

Within first 3 months:
- Generate awareness of Ballzcoin amongst miners and pool operators to build hash rate and establish a stable network.
- Release blockchain explorer.
- Release website and start distributing charity accounts.

Within first 6 months:
- All charity accounts distributed.
- Release web wallet.
- Update GUI wallets.
- First exchange listing.

Ongoing (anytime from initial release onwards):
- Incentivize the development and deployment of DApps on the Ballzcoin network by third parties.
- Periodic client updates to coincide with major upgrades and important bug fixes to the original Go Ethereum client.
- Additional exchange listings.
- Expansion of the Ballzcoin team, as required.
- Promotion and awareness of Ballzcoin to maintain the value of the underlying currency, and retain the support of miners in securing the network.


## Version History

- V1.0 - 8th September 2018

