# Cyber Foundation Aragon Setup Guide

## For what and for whom this guide
1. For Cyber community for whom we provide fully transperent flow of Foundation launch.
2. For Cybercongress as guide of setup process.
3. For Aragon developers which can find answers for their questions.
4. For Aragon community which may discover endless possibilities of Aragon project and stack

## Flow
0. Prepare you setup
1. Cyber Congress DAO
    1. Configuration
    2. Agent
    3. Grant Permission to transfer Agent their own tokens
2. Deploy Aragon Company Template
    1. Changes
    2. Deploy
    3. Verification
3. Vesting & Auction Aragon applications
    1. About Vesting application
    2. About Auction application
    3. Deploy to Aragon Package Manager (APM)
    4. Propagate application to IPFS
3. Cyber Foundation DAO
    1. Create proposal to deploy token by Congress Agent
    2. Create proposal to deploy DAO by Congress Agent
    3. Vote for token and DAO deploy by Cyber Congressmans
    4. Check Foundation DAO
    5. Voting by Congress Agent in Foundation
4. Install Vesting application to Foundation
    1. Parameters
    2. Install application
    3. Initialize Proof/Pause Roles
    4. Grant permission to Issue/Assign/Burn on Token Manager 
5. Install Auction application to Foundation
    1. Parameters
    2. Install
    3. Initialize Creator Role
    4. Grant permission for Burn on Token Manager
    5. Burn service's account token in Foundation DAO
    6. Transfer tokens from Congress Agent to Auction contract
    7. Load Auction
    8. Deploy and verify Auction Utils
    9. Add Auctin Utils address to Auction
7. Distribute tokens
    1. Inventors
    2. Investors
8. Final State
    1. Foundation DAO
    2. Vesting application
    3. Auction application
    4. Total transactions
    5. Total burned gas
9. Final thoughts
________

## Prepare you setup

Check [Troubleshooting and FAQ](https://hack.aragon.org/docs/guides-faq) guide

Recommended NodeJS version is v10.18.0.
If something not work try change between this two @aragon/cli version v6.4.4/v7.0.2 (global or local to project).

_________

## [Cyber Congress DAO](https://rinkeby.aragon.org/#/cc14)

### Configuration

Cyber Congress DAO is Aragon DAO bootrapped from official membership Aragon template with installed Agent. 
Use a non-transferable token to represent membership. Decisions are made based on one-member-one-vote governance. 

Signers:
- 0xd622E7279Db39B3a4807678BFBA3b792A81759ED
- 0x5eB63fC9837EAB21192eEB5250BE3EFd239b87f0
- 0xbb923dDA9DD20770B96Bc0231eE8631470e36402

Voting parameters
- Support: 51%
- Approval: 51% 
- Voting Duration: 7 days

Offical Documentation:
- [Official User Guide](help.aragon.org/article/34-create-a-new-membership-organization)
- [Source Code on GitHub](https://github.com/aragon/dao-templates/tree/templates-membership-v1.0.0/templates/membership)

### Agent

```
TO DO DESCRIBE
```


### Grant Permission to transfer Agent their own tokens

We need to grant access to agent's transfer tokens from their own account because during Foundation DAO setup we will issue the whole initial supply to them. It means that agents, which represent intention of Congress, will governance whole Foundation DAO during setup. After the bootstrap process tokens from the agent will be transferred to auction and then to distribution and to investors/investors. Part of them will continue to hold following Cyber tokens distribution agreement to Congress.

![congress-agent-token-permisson-create](./congress-agent-token-permisson-create.png)

![congress-agent-token-permissons](./congress-agent-token-permissons.png)

_________

## Deploy Aragon Company Template

Cyber Foundation will be DAO based on Company Aragon DAO Template. Use transferable tokens to represent ownership stake in DAO. Decisions are made based on stake-weighted voting. 

### Changes

We need modify official template and change default token's decimals value. We need token with __zero decimals__.

1. Clone official Aragon DAO-Templates [repository](https://github.com/aragon/dao-templates/):
```
git clone https://github.com/aragon/dao-templates
cd dao-templates/templates/company
```

2. Change [one line](https://github.com/aragon/dao-templates/blob/master/templates/company/contracts/CompanyTemplate.sol#L14) and set TOKEN_DECIMALS to 0:
```
uint8 constant private TOKEN_DECIMALS = uint8(0); 
```

Diff of CompanyTemplate.sol
```
--- a/templates/company/contracts/CompanyTemplate.sol
+++ b/templates/company/contracts/CompanyTemplate.sol
@@ -11,7 +11,7 @@ contract CompanyTemplate is BaseTemplate, TokenCache {
     string constant private ERROR_BAD_PAYROLL_SETTINGS = "COMPANY_BAD_PAYROLL_SETTINGS";

     bool constant private TOKEN_TRANSFERABLE = true;
-    uint8 constant private TOKEN_DECIMALS = uint8(18);
+    uint8 constant private TOKEN_DECIMALS = uint8(0);
     uint256 constant private TOKEN_MAX_PER_ACCOUNT = uint256(0);
     uint64 constant private DEFAULT_FINANCE_PERIOD = uint64(30 days);

@@ -30,7 +30,7 @@ contract CompanyTemplate is BaseTemplate, TokenCache {
     * @param _tokenSymbol String with the symbol for the token used by share holders in the organization
     * @param _id String with the name for org, will assign `[id].aragonid.eth`
     * @param _holders Array of token holder addresses
-    * @param _stakes Array of token stakes for holders (token has 18 decimals, multiply token amount `* 10^18`)
+    * @param _stakes Array of token stakes for holders (token has 0 decimals)
     * @param _votingSettings Array of [supportRequired, minAcceptanceQuorum, voteDuration] to set up the voting app of the organization
     * @param _financePeriod Initial duration for accounting periods, it can be set to zero in order to use the default of 30 days.
     * @param _useAgentAsVault Boolean to tell whether to use an Agent app as a more advanced form of Vault app
@@ -66,7 +66,7 @@ contract CompanyTemplate is BaseTemplate, TokenCache {
     * @dev Deploy a Company DAO using a previously cached MiniMe token
     * @param _id String with the name for org, will assign `[id].aragonid.eth`
     * @param _holders Array of token holder addresses
-    * @param _stakes Array of token stakes for holders (token has 18 decimals, multiply token amount `* 10^18`)
+    * @param _stakes Array of token stakes for holders (token has 0 decimals)
     * @param _votingSettings Array of [supportRequired, minAcceptanceQuorum, voteDuration] to set up the voting app of the organization
     * @param _financePeriod Initial duration for accounting periods, it can be set to zero in order to use the default of 30 days.
     * @param _useAgentAsVault Boolean to tell whether to use an Agent app as a more advanced form of Vault app
@@ -95,7 +95,7 @@ contract CompanyTemplate is BaseTemplate, TokenCache {
     * @dev Deploy a Company DAO using a previously cached MiniMe token
     * @param _id String with the name for org, will assign `[id].aragonid.eth`
     * @param _holders Array of token holder addresses
-    * @param _stakes Array of token stakes for holders (token has 18 decimals, multiply token amount `* 10^18`)
+    * @param _stakes Array of token stakes for holders (token has 0 decimals)
     * @param _votingSettings Array of [supportRequired, minAcceptanceQuorum, voteDuration] to set up the voting app of the organization
     * @param _financePeriod Initial duration for accounting periods, it can be set to zero in order to use the default of 30 days.
     * @param _useAgentAsVault Boolean to tell whether to use an Agent app as a more advanced form of Vault app
```

### Deploy
Command (cast tx):
```
npm run deploy:<network>
```
Output:
```
=========
# CompanyTemplate:
Address: 0x47a5f974312b254395d329d6de5b264e0579bbb1
Transaction hash: 0x7286d11d2276268c24b620f245b78e0e1c2bebe3d82e83ab55d6d1386684fb29
Compiler: solc@0.4.24+commit.e67f0147.Emscripten.clang (Optimizer: 10000 runs)
Compiled at: 2020-01-19T11:38:01.984Z
=========
```

### Verity on Etherscan
Prepare single file with needed code to verification.
```
truffle-flattener CompanyTemplate.sol > CompanyTemplateFull.sol
```
And then use Etherscan Verificatoin service to verify contract. Set optimization to enabled with __10000 runs__.

Link to verified template [contract](https://rinkeby.etherscan.io/address/0x47a5f974312b254395d329d6de5b264e0579bbb1#code) on Etherscan
__________

## Vesting & Auction Aragon applications

Repositories:
- [Vesting application](https://github.com/cybercongress/aragon-vesting-app)
- [Auction application](https://github.com/cybercongress/aragon-auction-app)

### About Vesting application
The Vesting app is used to vest your cyber~Foundation tokens until the end of cyber~Auction. It is also used to claim an equivalent, 1-to-1, amount of tokens in the Cyber blockchain (if the foundation tokens are vested). 

Your tokens will be locked for transfer for the duration of the auction. But, are available to be used for participating in the governance of the DAO.

After the end of the auction and the finalization of the distribution, the tokens will be unlocked for transfer. From this point onwards, cyber~Congress will no longer be responsible for their transfer.

Features:
- Vest and lock user tokens for transfers, until a set date
- Creation of claims
- Process user claims state

### About Auction application

```
TODO
```

Features:
- Buy tokens
- Claim tokens
- Collect ETH to foundation

### Deploy to Aragon Package Manager (APM)

This goes from project root directory and will publish packages to APM.
```
aragon apm publish major --environment rinkeby --files dist       

  ✔ Applying version bump (major)
  ✔ Building frontend
  ✔ Deploy contract
  ✔ Determine contract address for version
  ✔ Prepare files for publishing
  ✔ Generate application artifact
  ✔ Publish intent

 The following information will be published: 
 Contract address: 0xD99f6EE4341Cc3709c751e445C1968191DF1E4a6 
 Content (ipfs): QmcWSV9smZMn8LhnVk79TP6o3kzKQuZzNiKtJiK1YwZptX 

? Publish to cyberauction.open.aragonpm.eth repo Yes

  ✔ Publish cyberauction.open.aragonpm.eth

 Successfully published cyberauction.open.aragonpm.eth v4.0.0 : 

Transaction hash: 0xa7d25d8be85dd4a848edcf89414b587af0654196e5945847d67e33169704a38e 

? Propagate content (Y/n) 
```

Application's Frontend CIDs:
- Auction - [QmcWSV9smZMn8LhnVk79TP6o3kzKQuZzNiKtJiK1YwZptX]()
- Vesting - [Qmcz48Le7ZKYE4zX1e5eDc2ntyxKj294qG6NcUyBXMDLto]()

### Propagate application to IPFS
```
npx aragon ipfs propagate <CID>
```
Better to repeat propagation procedure couple of times.

```
aragon ipfs propagate Qme7yuXPU8ev3kx7jaH32ph4V9bTWm46V27bJju4QEviBa
  
  ✔ Check IPFS
  ✔ Connect to IPFS
  ✔ Fetch the links
  ✔ Query gateways

 Queried 29 CIDs at 7 gateways 
 Requests succeeded: 129 
 Requests failed: 74 
```
To provide good service need to pin also applications frontend to as much as available IPFS nodes, better to your own disributed IPFS cluster.

Recommend to add this peer to your node swarm:
```
ipfs swarm connect /ip4/134.209.179.153/tcp/4001/ipfs/QmU7j23HQx6Z3kYTnGv3YUxftVLUrtdt35nTdK5uKVajVu
```

After this Vesting application will be available to install from __cybervesting.open.aragonpm.eth__ and Auction from __cyberauction.open.aragonpm.eth__.

Note: If you fail with this step then try use @aragon/cli v7.0.2 for publish and v6.4.4 for propagation.

___________

## Cyber Foundation DAO

Here we will deploy Foundation DAO by Congress Agent. DAO deploy consist from two steps: deploy of token and DAO itself. This deploys requiers a lot of gas so there will be two transactions: newToken and newInstance which we will call on prepared template. During token deploy address of newly created token contract instance will be cached to caller and then this cache will be used to initialize DAO on next step.

The awesome thing there that DAO will be created by extra actor - Agent of Congress, which described before and will realize our intents.

### Create proposal to deploy token by Congress Agent
Command template (cast tx):
```
npx dao act <agentCongressAddress> <companyTemplateAddress> "newToken(string,string)" "<name>" "<ticker>" --environment aragon:<network>
```
Command (cast tx):
```
npx dao act 0x2d0970da60f36be926f78fbe8d6ef035eeaaf09f  0x47a5f974312b254395d329d6de5b264e0579bbb1 "newToken(string,string)" "tgol24" "TGOL24" --environment aragon:rinkeby
```
Output:
```
  ⠴ Executing execute on 0x9cE69C31F98827eE5B8602988E07F8A0Af787035
  ✔ Executing execute on 0x9cE69C31F98827eE5B8602988E07F8A0Af787035

✔ Successfully executed: "Execute desired action as a token holder"
```

### Create proposal to deploy DAO by Congress Agent

Notes:
- Use holders addresses in params without 0x
- Use ```"[\"<value>\", \"<value>\"]"``` for arrays 
- For support and approval ```X%``` is ```X0000000000000000``` (ex: 500000000000000000 is 50%)
- Vor vote duration time in seconds (ex: 1200 is 20 minutes)
- Finance period set default to 0
- Install Agent with true

Template of Transaction:
```
dao act <agentCongressAddress> <companyTemplateAddress> "newInstance(string,address[],uint256[],uint64[3],uint64,bool)" "<nameDAO>"  "[\"<holder1>\"]" "[\"<balance1>\"]" "[\"<support>\",\"<approval>\",\"<timeToVote>\"]" <financePeriod> <installAgent> --environment aragon:<network>
```

Note on Voting params values:
```
TODO
```
Params:
- Supply: 700T TGOL22 + 1 TGOL22 (for proposals, will be burned)
- Support: 51% (500000000000000000)
- Approval: 20% (200000000000000000)
- Voting time: 2 weeks (1209600)

Note on extra account:

Attention. We issue the whole needed supply to congress's agent but also adding here 1 extra account with only 1 token. This needed because now possible but very hard to construct a set of transactions that allow to the agent of one dao as the token holder in another dao guide agent of this dao for actions. And all this should turn around proposals for each action... Our solution to not over complicate set up and use a service account for each will be issued 1 extra token (which will be burned after he completes his mission) and this will allow them to create transactions in Foundation DAO and create a proposals too. But their weight will not allow influencing any proposal (and this not needed).

Command (cast tx):
```
dao act 0x2d0970da60f36be926f78fbe8d6ef035eeaaf09f 0x47a5f974312b254395d329d6de5b264e0579bbb1 "newInstance(string,address[],uint256[],uint64[3],uint64,bool)" "tgol24" "[\"5eb63fc9837eab21192eeb5250be3efd239b87f0\",\"2d0970da60f36be926f78fbe8d6ef035eeaaf09f\"]" "[\"1\",\"700000000000000\"]" "[\"500000000000000000\",\"200000000000000000\",\"1209600\"]" 0 true --environment aragon:rinkeby
```

Output:
```
  ⠼ Executing execute on 0x9cE69C31F98827eE5B8602988E07F8A0Af787035
  ✔ Executing execute on 0x9cE69C31F98827eE5B8602988E07F8A0Af787035

✔ Successfully executed: "Execute desired action as a token holder"
```

### Vote for token and DAO deploy by Cyber Congressmans

![congress-create-dao](./congress-create-dao.png)
![congress-vote-0](./congress-vote-0.png)
![congress-vote-1](./congress-vote-1.png)
![congress-create-dao-passed](./congress-create-dao-passed.png)

After proposal passed new DAO [available](https://rinkeby.aragon.org/#/tgol24)
![foundation-created](./foundation-created.png)

First and major holder here is Congress' Agent and second one is service account which was described before.

### Check Foundation DAO
This command allows you to check installed applications and addresses of their instances also check a permissionless application (which installed and initialized but need to set up needed permission to start their operations). 

Template of command:
```
npx dao apps <kernelFoundationAddress> --all --environment aragon:<network>
```

Command (not cast tx):
```
npx dao apps 0x7eFA8E568a5fE91741f72A39b96f42EEdB67C419 --all --environment aragon:rinkeby
```
Output:
```
  ⠧ Inspecting DAO
  ✔ Inspecting DAO
  ✔ Fetching permissionless apps

✔ Successfully fetched DAO apps for 0x7eFA8E568a5fE91741f72A39b96f42EEdB67C419
┌──────────────────────┬────────────────────────────────────────────┬─────────────────────────────────────────────────────┐
│ App                  │ Proxy address                              │ Content                                             │
├──────────────────────┼────────────────────────────────────────────┼─────────────────────────────────────────────────────┤
│ kernel               │ 0x7eFA8E568a5fE91741f72A39b96f42EEdB67C419 │ (No UI available)                                   │
├──────────────────────┼────────────────────────────────────────────┼─────────────────────────────────────────────────────┤
│ acl                  │ 0xb7caf90de7679c4ab0067b93a2cc0871a8b968e0 │ (No UI available)                                   │
├──────────────────────┼────────────────────────────────────────────┼─────────────────────────────────────────────────────┤
│ evmreg               │ 0xc3616c8ceb147352cf4c0e7fe0728cb6ea64df09 │ (No UI available)                                   │
├──────────────────────┼────────────────────────────────────────────┼─────────────────────────────────────────────────────┤
│ token-manager@v2.1.7 │ 0x980db5f2aa983c29e47442cbef9ae4654b726534 │ ipfs:QmdLEDDfiH3RGjbgU5hqJwwALbNreMWupqTQCRGfNrMPiP │
├──────────────────────┼────────────────────────────────────────────┼─────────────────────────────────────────────────────┤
│ agent@v4.1.1         │ 0xd522a7729e57d22d1a868f92aae57b902b858a4d │ ipfs:QmYUo7bYzCPATX8hL8RFQcYviRscnvHALZWPGph7qBYy3c │
├──────────────────────┼────────────────────────────────────────────┼─────────────────────────────────────────────────────┤
│ finance@v2.1.7       │ 0x7df6821dc639d5d4fe4f8ec424de72e155835e57 │ ipfs:QmbAEvts3zkDKM71k5n45EsS4fEqXi3958SFedJUveDJ2D │
├──────────────────────┼────────────────────────────────────────────┼─────────────────────────────────────────────────────┤
│ voting@v2.1.7        │ 0x519bc4f0b24f9bf5c093f7cfdaf962a3bd15f414 │ ipfs:QmUvWBZqpKepDwS5WUsTTURwiBRvhoGRTjwJG7Em5TU2UJ │
└──────────────────────┴────────────────────────────────────────────┴─────────────────────────────────────────────────────┘
```

### Voting by Congress Agent in Foundation
Create proposal to vote YES/NO in proposal in another DAO
```
dao act <agentCongressAddress> <foundationVotingAppAddress>  "vote(uint256,bool,bool)" <id> <vote> <enact>  --environment aragon:<network>
```
Note: to forward intent of Congressmen to another DAO will be used Agent of Congress. One of congressman's creates proposal to vote yes or no for giving proposals in another DAO and call Congress Agent for this giving them voting application instance of another DAO and proposal ID and decision which need to apply. After the proposal passing agent will forward a vote to the given proposal in another DAO.
____________

## Install Vesting application to Foundation

## Parameters
Params:
```
address _tokenManager, 
uint64 _vestingEnd
```

Params:
- Token Manager - 0x980db5f2aa983c29e47442cbef9ae4654b726534
- Vesting End - Thursday, February 20, 2020 12:00:00 AM GMT (1582156800)

Note: Vesting application needs token manager address of Foundation DAO because it creates vestings (token manager functionality) and call issue, assing and burns operation on the token manager. Vesting end time it is time when given vesting's token will be allowed for transfer again. Time set up follow distribution agreement (needs to auction to be completed) plus a certain amount of time (window for holders to make vestings after whole auction end and dust from the auction will provable burned by Congress). After vesting end time Congress will stop its commitment to provide claims for Cyb tokens in the Cyber chain.

### Install application 

Note: this action goes by service account

Command template (cast tx):
```
npx dao install <kernelFoundationAddress> cyberclaim.open.aragonpm.eth latest --app-init-args <tokenManagerFoundationAddress> <vestingEnd> --environment aragon:rinkeby
```

Command (cast tx):
```
npx dao install 0x7eFA8E568a5fE91741f72A39b96f42EEdB67C419 cybervesting.open.aragonpm.eth latest --app-init-args 0x980db5f2aa983c29e47442cbef9ae4654b726534 1582156800 --environment aragon:rinkeby
```
Output:
```
  ✔ Fetching cybervesting.open.aragonpm.eth@latest
  ✔ Fetching cybervesting.open.aragonpm.eth@latest
  ↓ Checking installed version [skipped]
    → Installing the first instance of cybervesting.open.aragonpm.eth in DAO
  ✔ Deploying app instance
  ↓ Fetching deployed app [skipped]
    → App wasn't deployed in transaction.

ℹ Successfully executed: "Execute desired action as a token holder"

⚠ After the app instance is created, you will need to assign permissions to it for it appear as an app in the DAO
```

Proposal to install Vesting application created in Foundation DAO:
![vesting-install-proposal](./vesting-install-proposal.png)


After that we need to vote for this proposal with Congress agent. One of congressmans creates proposal to vote YES for this proposal in Foundation DAO in Congress DAO.

Command template (cast tx):
```
dao act <agentCongressAddress> <foundationVotingAddress>  "vote(uint256,bool,bool)" <id> <vote> <enact>  --environment aragon:<network>
```
Command (cast tx):
```
dao act 0x2d0970da60f36be926f78fbe8d6ef035eeaaf09f 0x519bc4f0b24f9bf5c093f7cfdaf962a3bd15f414 "vote(uint256,bool,bool)" 0 true true  --environment aragon:rinkeby
```
Output:
```
  ⠼ Executing execute on 0x3c36dB0ecf710313De0494A47bA83663e32543e7
  ✔ Executing execute on 0x3c36dB0ecf710313De0494A47bA83663e32543e7

✔ Successfully executed: "Execute desired action as a token holder"
```

Here congressmans votes YES to proposal in Foundation DAO to install Vesting application in cyber~Congress DAO:
![congress-vote-2-0](./congress-vote-2-0.png)

Proposal passed in Foundation DAO:
![vesting-install-passed](./vesting-install-passed.png)


Note: Now Vesting application is permissionless. To complete installation process we need to assign permissons to Vesting application.
```
  ⠇ Inspecting DAO
  ✔ Inspecting DAO
  ✔ Fetching permissionless apps

✔ Successfully fetched DAO apps for 0x7eFA8E568a5fE91741f72A39b96f42EEdB67C419
┌──────────────────────┬────────────────────────────────────────────┬─────────────────────────────────────────────────────┐
│ App                  │ Proxy address                              │ Content                                             │
├──────────────────────┼────────────────────────────────────────────┼─────────────────────────────────────────────────────┤
│ kernel               │ 0x7eFA8E568a5fE91741f72A39b96f42EEdB67C419 │ (No UI available)                                   │
├──────────────────────┼────────────────────────────────────────────┼─────────────────────────────────────────────────────┤
│ acl                  │ 0xb7caf90de7679c4ab0067b93a2cc0871a8b968e0 │ (No UI available)                                   │
├──────────────────────┼────────────────────────────────────────────┼─────────────────────────────────────────────────────┤
│ evmreg               │ 0xc3616c8ceb147352cf4c0e7fe0728cb6ea64df09 │ (No UI available)                                   │
├──────────────────────┼────────────────────────────────────────────┼─────────────────────────────────────────────────────┤
│ token-manager@v2.1.7 │ 0x980db5f2aa983c29e47442cbef9ae4654b726534 │ ipfs:QmdLEDDfiH3RGjbgU5hqJwwALbNreMWupqTQCRGfNrMPiP │
├──────────────────────┼────────────────────────────────────────────┼─────────────────────────────────────────────────────┤
│ agent@v4.1.1         │ 0xd522a7729e57d22d1a868f92aae57b902b858a4d │ ipfs:QmYUo7bYzCPATX8hL8RFQcYviRscnvHALZWPGph7qBYy3c │
├──────────────────────┼────────────────────────────────────────────┼─────────────────────────────────────────────────────┤
│ finance@v2.1.7       │ 0x7df6821dc639d5d4fe4f8ec424de72e155835e57 │ ipfs:QmbAEvts3zkDKM71k5n45EsS4fEqXi3958SFedJUveDJ2D │
├──────────────────────┼────────────────────────────────────────────┼─────────────────────────────────────────────────────┤
│ voting@v2.1.7        │ 0x519bc4f0b24f9bf5c093f7cfdaf962a3bd15f414 │ ipfs:QmUvWBZqpKepDwS5WUsTTURwiBRvhoGRTjwJG7Em5TU2UJ │
└──────────────────────┴────────────────────────────────────────────┴─────────────────────────────────────────────────────┘
┌────────────────────────────────────────────────────────────────────┬────────────────────────────────────────────┐
│ Permissionless app                                                 │ Proxy address                              │
├────────────────────────────────────────────────────────────────────┼────────────────────────────────────────────┤
│ 0xb3898b383ac8a6bae742b41163ced5666086e3e367be1b3de9251c2dbc45d412 │ 0x1A3B7ce3108913454711a4f78a15F139946675f0 │
└────────────────────────────────────────────────────────────────────┴────────────────────────────────────────────┘
```

### Initialize Proof/Pause Roles

__Pause Role__ - assigning this to Congress Agent will allow Congress following aggrement stop vestings and claiming of CYB following crisis protocol;
```
TO DO Think may be we should change permission manager to Agent itself? (now we set voting application of Foundation as permission manager)
```

__Proof Role__ - assigning this to Congress driven external address will allow Congress send tokens and then write transactions as proofs to given vestings created by agents.
```
TO DO Think may be we set not one but set of addresses to this role in case of compromising this external address.
Or maybe set permission manager to Congress agent itself ((now we set voting application of Foundation as permission manager)
```

Command template (cast tx):
```
npx dao acl create <kernelFoundationAddress> <vestingApplicationAddress> PAUSE_ROLE <agentCongressAddress> <foundationVotingAppAddress> --environment aragon:<network>
```

Command (cast tx):
```
npx dao acl create 0x7eFA8E568a5fE91741f72A39b96f42EEdB67C419 0x1A3B7ce3108913454711a4f78a15F139946675f0 PAUSE_ROLE 0x2d0970da60F36BE926f78FBE8D6ef035EEaAF09f 0x519bc4f0b24f9bf5c093f7cfdaf962a3bd15f414 --environment aragon:rinkeby
```
Output:
```
  ⠼ Executing createPermission on 0x7eFA8E568a5fE91741f72A39b96f42EEdB67C419
  ✔ Executing createPermission on 0x7eFA8E568a5fE91741f72A39b96f42EEdB67C419

✔ Successfully executed: "Execute desired action as a token holder"
``` 

Command template (cast tx):
```
npx dao acl create <kernelFoundationAddress> <claimAppAddress> PROOF_ROLE <prooferAddress> <foundationVotingAppAddress> --environment aragon:<network>
```

Command (cast tx):
```
npx dao acl create 0x7eFA8E568a5fE91741f72A39b96f42EEdB67C419 0x1A3B7ce3108913454711a4f78a15F139946675f0 PROOF_ROLE 0x5eB63fC9837EAB21192eEB5250BE3EFd239b87f0 0x519bc4f0b24f9bf5c093f7cfdaf962a3bd15f414 --environment aragon:rinkeby
```
Output:
```
  ⠼ Executing createPermission on 0x7eFA8E568a5fE91741f72A39b96f42EEdB67C419
  ✔ Executing createPermission on 0x7eFA8E568a5fE91741f72A39b96f42EEdB67C419

✔ Successfully executed: "Execute desired action as a token holder"
```

Created proposals in Foundation DAO:
![vesting-permissions-proposals](./vesting-permissons-proposals.png)

Create proposal to vote:
```
dao act 0x2d0970da60f36be926f78fbe8d6ef035eeaaf09f 0x519bc4f0b24f9bf5c093f7cfdaf962a3bd15f414 "vote(uint256,bool,bool)" 1 true true  --environment aragon:rinkeby
```
Create proposal to vote:
```
dao act 0x2d0970da60f36be926f78fbe8d6ef035eeaaf09f 0x519bc4f0b24f9bf5c093f7cfdaf962a3bd15f414 "vote(uint256,bool,bool)" 2 true true  --environment aragon:rinkeby
```

Created proposals in Congress DAO:
![congress-votes-3-1-4-2-passed](./congress-votes-3-1-4-2.png)

Passed proposals which forward YESs votings to Fondation DAO:

![congress-votes-3-1-4-2-passed](./congress-votes-3-1-4-2-passed.png)

Passed proposals in Foundation DAO:
![vesting-permissions-proposals-passed](./vesting-permissions-proposals-passed.png)
Here we see that there is cyber~Vesting application now available.

Permissions of Auction in Foundation DAO:
![foundation-vesting-permissons](./foundation-vesting-permssions.png)

## Grant permission to Issue/Assign/Burn on Token Manager 

Now we need to grant needed permissions to Vesting application for use needed function of Token Manager of Foundation DAO.

Note: This action goes from frontend application

1. Issue - initialize with entity Vesting Application and Voting application as manager
2. Assing - initialize with entity Vesting Application and Voting application as manager
3. Burn - grant permission to entity Vesting Application 

![token-manager-1](./token-manager-vesting-1.png)
![token-manager-issue-vesting](./token-manager-issue-vesting.png)
![token-manager-assign-vesting](./token-manager-assign-vesting.png)
![token-manager-burn-vesting](./token-manager-burn-vesting.png)

![token-manager-vesting-proposals](./token-manager-vesting-proposals.png)

After that create 3 votings:
```
dao act <agentCongressAddress> <foundationVotingAddress>  "vote(uint256,bool,bool)" <id> <vote> <enact>  --environment aragon:<network>
```
```
dao act 0x2d0970da60f36be926f78fbe8d6ef035eeaaf09f 0x519bc4f0b24f9bf5c093f7cfdaf962a3bd15f414 "vote(uint256,bool,bool)" 3 true true  --environment aragon:rinkeby
```
```
dao act 0x2d0970da60f36be926f78fbe8d6ef035eeaaf09f 0x519bc4f0b24f9bf5c093f7cfdaf962a3bd15f414 "vote(uint256,bool,bool)" 4 true true  --environment aragon:rinkeby
```
```
dao act 0x2d0970da60f36be926f78fbe8d6ef035eeaaf09f 0x519bc4f0b24f9bf5c093f7cfdaf962a3bd15f414 "vote(uint256,bool,bool)" 5 true true  --environment aragon:rinkeby
```
Ouputs:
```
  ⠼ Executing execute on 0x9cE69C31F98827eE5B8602988E07F8A0Af787035
  ✔ Executing execute on 0x9cE69C31F98827eE5B8602988E07F8A0Af787035

✔ Successfully executed: "Execute desired action as a token holder"
```
![congress-vote-token-manager-vesting](./congress-vote-token-manager-vesting.png)

Congressmans votes on this proposals and forward YES votes to proposals which sets permission on Token Manager in Foundation DAO granting following permissions to Vesting application.

![congress-vote-token-manager-vesting-passed](./congress-vote-token-manager-vesting-passed.png)

Proposals passed in Foundation DAO:

![foundation-token-mananger-vesting-proposals-passed](./foundation-token-mananger-vesting-proposals-passed.png)

New permissions on Token Manager in Foundation DAO:
![token-manager-vesting-2](./token-manager-vesting-2.png)

Installation of Vesting application in Foundation DAO is completed, application is fully operational.
____________

## Install Auction application to Foundation

### Parameters

Parameters:
```
uint256 _numberOfDays,
uint256 _openTime,
uint256 _startTime,
MiniMeToken _token,
address _foundation,
address _tokenManager
```

- Number of days is amount of windows/rounds of given time (23 hours + 1 second) auction will continue after zero window.
- Open time is time when zero round will begin (Ethreum's part of Game of Thrones)
- Start time is time when zero found will be finished and equal time rounds will start.
- Foundation address is address to which collected Ethers will be transferred (Foundation's DAO agent)

```
- numberOfDays - 20
- openTime -  1581355800 Monday, February 10, 2020 5:30:00 PM
- startTime - 1581692400 Friday, February 14, 2020 3:00:00 PM GMT+0
- token - Foundation token TGOL24 (0x3638309F7fbBF5AF5F016Eb48e3Dbfa43C44d9cA)
- foundation - Agent of Foundation (0xd522a7729e57d22d1a868f92aae57b902b858a4d)
```

### Install
Command template (cast tx):
```
npx dao install <kernel> cyberauction.open.aragonpm.eth latest --app-init-args <windows> <open> <start> <token> <agentFoundationAddress> <tokenManagerAddress> --environment aragon:rinkeby
```
Command (cast tx):
```
npx dao install 0x7eFA8E568a5fE91741f72A39b96f42EEdB67C419 cyberauction.open.aragonpm.eth latest --app-init-args 20 1581355800 1581692400 0x3638309F7fbBF5AF5F016Eb48e3Dbfa43C44d9cA 0xd522a7729e57d22d1a868f92aae57b902b858a4d  0x980db5f2aa983c29e47442cbef9ae4654b726534 --environment aragon:rinkeby
```
Output:
```
  ✔ Fetching cyberauction.open.aragonpm.eth@latest
  ✔ Fetching cyberauction.open.aragonpm.eth@latest
  ↓ Checking installed version [skipped]
    → Installing the first instance of cyberauction.open.aragonpm.eth in DAO
  ✔ Deploying app instance
  ↓ Fetching deployed app [skipped]
    → App wasn't deployed in transaction.

ℹ Successfully executed: "Execute desired action as a token holder"

⚠ After the app instance is created, you will need to assign permissions to it for it appear as an app in the DAO
```

Proposal to install Auction in Foundation DAO created:
![foundation-auction-install-proposal](./foundation-auction-install-proposal.png)

### After create voting in for vote YES by Congress' Agent in Congress and Vote
Command (cast tx):
```
dao act 0x2d0970da60f36be926f78fbe8d6ef035eeaaf09f 0x519bc4f0b24f9bf5c093f7cfdaf962a3bd15f414 "vote(uint256,bool,bool)" 6 true true  --environment aragon:rinkeby
```
Created proposal to vote YES in Congress DAO:
![congress-vote-install-auction](./congress-vote-install-auction.png)

Passed proposal in Congress DAO:
![congress-vote-install-auction-passed](./congress-vote-install-auction-passed.png)

Passed proposal in Foundation DAO to install Auction application:
![congress-vote-install-auction-passed](./congress-vote-install-auction-passed.png)

```
➜  ~ npx dao apps 0x7eFA8E568a5fE91741f72A39b96f42EEdB67C419 --all --environment aragon:rinkeby
  ⠋ Inspecting DAO
  ✔ Inspecting DAO
  ✔ Fetching permissionless apps

✔ Successfully fetched DAO apps for 0x7eFA8E568a5fE91741f72A39b96f42EEdB67C419
┌──────────────────────────┬────────────────────────────────────────────┬─────────────────────────────────────────────────────┐
│ App                      │ Proxy address                              │ Content                                             │
├──────────────────────────┼────────────────────────────────────────────┼─────────────────────────────────────────────────────┤
│ kernel                   │ 0x7eFA8E568a5fE91741f72A39b96f42EEdB67C419 │ (No UI available)                                   │
├──────────────────────────┼────────────────────────────────────────────┼─────────────────────────────────────────────────────┤
│ acl                      │ 0xb7caf90de7679c4ab0067b93a2cc0871a8b968e0 │ (No UI available)                                   │
├──────────────────────────┼────────────────────────────────────────────┼─────────────────────────────────────────────────────┤
│ evmreg                   │ 0xc3616c8ceb147352cf4c0e7fe0728cb6ea64df09 │ (No UI available)                                   │
├──────────────────────────┼────────────────────────────────────────────┼─────────────────────────────────────────────────────┤
│ token-manager@v2.1.7     │ 0x980db5f2aa983c29e47442cbef9ae4654b726534 │ ipfs:QmdLEDDfiH3RGjbgU5hqJwwALbNreMWupqTQCRGfNrMPiP │
├──────────────────────────┼────────────────────────────────────────────┼─────────────────────────────────────────────────────┤
│ agent@v4.1.1             │ 0xd522a7729e57d22d1a868f92aae57b902b858a4d │ ipfs:QmYUo7bYzCPATX8hL8RFQcYviRscnvHALZWPGph7qBYy3c │
├──────────────────────────┼────────────────────────────────────────────┼─────────────────────────────────────────────────────┤
│ finance@v2.1.7           │ 0x7df6821dc639d5d4fe4f8ec424de72e155835e57 │ ipfs:QmbAEvts3zkDKM71k5n45EsS4fEqXi3958SFedJUveDJ2D │
├──────────────────────────┼────────────────────────────────────────────┼─────────────────────────────────────────────────────┤
│ voting@v2.1.7            │ 0x519bc4f0b24f9bf5c093f7cfdaf962a3bd15f414 │ ipfs:QmUvWBZqpKepDwS5WUsTTURwiBRvhoGRTjwJG7Em5TU2UJ │
├──────────────────────────┼────────────────────────────────────────────┼─────────────────────────────────────────────────────┤
│ cybervesting.open@v5.0.0 │ 0x1a3b7ce3108913454711a4f78a15f139946675f0 │ ipfs:Qmcz48Le7ZKYE4zX1e5eDc2ntyxKj294qG6NcUyBXMDLto │
└──────────────────────────┴────────────────────────────────────────────┴─────────────────────────────────────────────────────┘
┌────────────────────────────────────────────────────────────────────┬────────────────────────────────────────────┐
│ Permissionless app                                                 │ Proxy address                              │
├────────────────────────────────────────────────────────────────────┼────────────────────────────────────────────┤
│ 0x7d9a0e9523a9f617c1b16588b4c2365cd331ca2e83ba785d79136358163c18a0 │ 0xC84B50DB399445a488450B2F4C6a15dd6B05eaE3 │
└────────────────────────────────────────────────────────────────────┴────────────────────────────────────────────┘
```
We need to initialize Auction application permissions and grant needed permissions to Token Manager.

## Initialize Creator Role

Here we assign Creator Role (which set up auction) to Congress' Agent. Creator also can (and should) burn dust tokens after auction end.

Command template (cast tx):
```
npx dao acl create <kernelFoundationAddress> <auctionApplicationAddress> CREATOR_ROLE <agentCongress> <foundationVotingAppAddress> --environment aragon:rinkeby
```
Command (cast tx):
```
npx dao acl create 0x7eFA8E568a5fE91741f72A39b96f42EEdB67C419 0xC84B50DB399445a488450B2F4C6a15dd6B05eaE3 CREATOR_ROLE 0x2d0970da60f36be926f78fbe8d6ef035eeaaf09f 0x519bc4f0b24f9bf5c093f7cfdaf962a3bd15f414 --environment aragon:rinkeby
```
Output:
```
  ⠏ Executing createPermission on 0x7eFA8E568a5fE91741f72A39b96f42EEdB67C419
  ✔ Executing createPermission on 0x7eFA8E568a5fE91741f72A39b96f42EEdB67C419

✔ Successfully executed: "Execute desired action as a token holder"
```
![foundation-auction-creator-proposal](./foundation-auction-creator-proposal.png)

### After create voting in for vote YES by Congress' Agent in Congress and Vote
Command (cast tx):
```
dao act 0x2d0970da60f36be926f78fbe8d6ef035eeaaf09f 0x519bc4f0b24f9bf5c093f7cfdaf962a3bd15f414 "vote(uint256,bool,bool)" 7 true true  --environment aragon:rinkeby
```
Output:
```
  ⠼ Executing execute on 0x9cE69C31F98827eE5B8602988E07F8A0Af787035
  ✔ Executing execute on 0x9cE69C31F98827eE5B8602988E07F8A0Af787035

✔ Successfully executed: "Execute desired action as a token holder"
```
Created proposal in Congress DAO:
![congress-auction-creator-proposal](./congress-auction-creator-proposal.png)

Passed proposal in Congress DAO and intent forwarded to Foundation DAO:
![congress-auction-creator-proposal-passed](./congress-auction-creator-proposal-passed.png)

Passed proposal and Auction installed in Foundation DAO:
![foundation-auction-creator-proposal-passed](./foundation-auction-creator-proposal-passed.png)

Permissions of Auction in Foundation DAO:
![foundation-auction-permissons](./foundation-auction-permissons.png)

## Grant permission for Burn on Token Manager

Auction should be able to burn tokens (dust) after their end. This role assigned to Congress Agent but we need to grant permission on Token Manager to Auction.

Grant to burn on Token Manager for Auction application in Foundation DAO:
![foundation-auction-burn-permission](./foundation-auction-burn-permission.png)

Created proposal to grant burn permission on Token Manager for Auction in Foundation DAO:
![foundation-auction-burn-proposal](./foundation-auction-burn-proposal.png)

We need to create proposal to vote YES on following proposal in Foundation DAO in Congress DAO:
Command (cast tx):
```
dao act 0x2d0970da60f36be926f78fbe8d6ef035eeaaf09f 0x519bc4f0b24f9bf5c093f7cfdaf962a3bd15f414 "vote(uint256,bool,bool)" 8 true true  --environment aragon:rinkeby
```
Output:
```
  ⠏ Executing execute on 0x9cE69C31F98827eE5B8602988E07F8A0Af787035
  ✔ Executing execute on 0x9cE69C31F98827eE5B8602988E07F8A0Af787035

✔ Successfully executed: "Execute desired action as a token holder"
```
Created proposal in Congress DAO:
![congress-auction-burn-proposal](./congress-auction-burn-proposal.png)

Proposal passed in Congress DAO and forwarded to Foundation DAO:
![congress-auction-burn-proposal-passed](./congress-auction-burn-proposal-passed.png)

Proposal passed in Foundation DAO:
![congress-auction-burn-proposal-passed](./congress-auction-burn-proposal-passed.png)

Auction have permission to burn tokens on Token Manager in Foundation DAO:
![foundation-token-manager-auction-burn](./foundation-token-manager-auction-burn.png)

### Burn service's account token in Foundation DAO

Note: Service account complete his mission of Foundation setup and Auction and Vesting applications and permissions installation. Finalization of Auction setup will be be completed by Congress itself because Congress' Agent have needed direct permission (CREATOR_ROLE) on Foundation' Auction.

Remove tokens of service account:
![foundation-burn-service-account](./foundation-burn-service-account.png)

Create transaction and proposal to remove 1 token from service account:
![foundation-burn-service-account-action](./foundation-burn-service-account-action.png)

Created proposal to burn 1 token from service account in Foundation DAO:
![foundation-burn-service-account-proposal](./foundation-burn-service-account-proposal.png)

Need to create proposal to vote YES on proposal to burn 1 token from service account in Foundation DAO in Congress DAO:
Command (cast tx):
```
dao act 0x2d0970da60f36be926f78fbe8d6ef035eeaaf09f 0x519bc4f0b24f9bf5c093f7cfdaf962a3bd15f414 "vote(uint256,bool,bool)" 9 true true  --environment aragon:rinkeby
```

Output:
```
  ⠇ Executing execute on 0x9cE69C31F98827eE5B8602988E07F8A0Af787035
  ✔ Executing execute on 0x9cE69C31F98827eE5B8602988E07F8A0Af787035

✔ Successfully executed: "Execute desired action as a token holder"
```

Created proposal in Congress DAO:
![congress-burn-service-account-proposal](./congress-burn-service-account-proposal.png)

Passsed proposal in Congress DAO:
![congress-burn-service-account-proposal-passed](./congress-burn-service-account-proposal-passed.png)

Passsed proposal for burn 1 token from service account in Foundation DAO:
![congress-burn-service-account-proposal-passed](./congress-burn-service-account-proposal-passed.png)

Whole supply of Foundation DAO now follows distribution aggrement:
![foundation-service-account-burned](./foundation-service-account-burned.png)

## Transfer tokens from Congress Agent to Auction contract

Now we can transfer needed amount of tokens to Auction for distribution following aggrement.
600 TGOL24 will be transffered from Congress' Agent to Foundation's Auction. Congress' Agent have permission for transfer their own token which we prepared before in Congress.

Command template (cast tx):
```
npx dao act <agentCongress> <agentCongress> "transfer(address,address,uint256)" <tokenFoundationAddress> <auctionApplicationAddress> <amount> --environment aragon:rinkeby
```
Command (cast tx):
```
npx dao act 0x2d0970da60F36BE926f78FBE8D6ef035EEaAF09f 0x2d0970da60F36BE926f78FBE8D6ef035EEaAF09f "transfer(address,address,uint256)" 0x3638309F7fbBF5AF5F016Eb48e3Dbfa43C44d9cA 0xc84b50db399445a488450b2f4c6a15dd6b05eae3 600000000000000  --environment aragon:rinkeby
```
Output:
```
  ⠧ Executing execute on 0x9cE69C31F98827eE5B8602988E07F8A0Af787035
  ✔ Executing execute on 0x9cE69C31F98827eE5B8602988E07F8A0Af787035

✔ Successfully executed: "Execute desired action as a token holder"
```

Created proposal to transfer supply from Congress' Agent to Auction in Congress DAO:
![congress-agent-trasnfer-auction-proposal](./congress-agent-trasnfer-auction-proposal.png)

Passed proposal to transfer supply from Congress' Agent to Auction in Congress DAO:
![congress-agent-trasnfer-auction-proposal-passed](./congress-agent-trasnfer-auction-proposal-passed.png)

Supply transffered to Auction in Foundation DAO:
![foundation-auction-supply-transffered](./foundation-auction-supply-transffered.png)

Now Foundation disribution completed and Auction have supply for disribution. Congress, Investors and Inventors of Cyber may participated in Foundation governance with proportion of tokens which they have following aggrement and Cyber whitepaper.

## Load Auction

Last step with Auction setup. Supply already transffered and we may load Auction give amount of Auction tokens to be distributed during zero window (Ethereum part of Game of Thrones). Following aggrement there will be 200T TGOL24. The remaining amount will be equaly split for other rounds (20 rounds of 23 hours + 1 sec).

Note: Congress' Agent have permission to directly do this action on Foundtation' Auction as CREATOR_ROLE.

Command template (cast tx):
```
npx dao act <agentCongress> <auctionApplicationAddress> "load(uint256)" <amount> --environment aragon:rinkeby
```
Command (cast tx):
```
npx dao act 0x2d0970da60F36BE926f78FBE8D6ef035EEaAF09f 0xC84B50DB399445a488450B2F4C6a15dd6B05eaE3 "load(uint256)" 200000000000000 --environment aragon:rinkeby
```
Output:
```
  ⠼ Executing execute on 0x9cE69C31F98827eE5B8602988E07F8A0Af787035
  ✔ Executing execute on 0x9cE69C31F98827eE5B8602988E07F8A0Af787035

✔ Successfully executed: "Execute desired action as a token holder"
```

Created proposal to load Foundation's Auction in Congress DAO:
![congress-auction-load-proposal](./congress-auction-load-proposal.png)

Passed proposal to load Foundation's Auction in Congress DAO:
![congress-auction-load-proposal-passed](./congress-auction-load-proposal-passed.png)

## Deploy and verify Auction Utils

Extra step for Auction. We deploy additional contract which will provide robust API to query Auction data from Auction Contract. And after contract deploy we will add AuctionUtils contract address to Auction (which will provide point to access AuctionUtils from Auction). Cyber Page appilication use Auction Utils contract to provide stats of ongoing Auction (Aragon DApps by design uses event driven state processing and don't need this).

Code:
```
contract AuctionUtils {
    Auction public sale;

    constructor(Auction _sale) public {
        sale = _sale;
    }

    function dailyTotals() external view returns (uint[21] memory result) {
        for (uint i = 0; i < 21; i++) {
            result[i] = sale.dailyTotals(i);
        }
    }

    function userBuys(address user) external view returns (uint[21] memory result) {
        for (uint i = 0; i < 21; i++) {
            result[i] = sale.userBuys(i, user);
        }
    }

    function userClaims(address user) external view returns (bool[21] memory result) {
        for (uint i = 0; i < 21; i++) {
            result[i] = sale.claimed(i, user);
        }
    }
}
```

We will use Remix IDE for Auction Utils deployment:
Compiled Auction Utils in Remix IDE:
![auction-utils-compile](./auction-utils-compile.png)

Deploy of Auction Utils:
![auction-utils-deploy](./auction-utils-deploy.png)

Auction Utils deployed with initialized Auction address:
![auction-utils-deploy](./auction-utils-deployed.png)

Auction Utils contract [0xee2eb45b07CdcD488933d1614119434AB3db78e7](https://rinkeby.etherscan.io/address/0xee2eb45b07cdcd488933d1614119434ab3db78e7)

Let's verify contract. We will use single file provided by truffle-flattener. Compiler v0.4.24+commit.e67f0147 without optimization.

Prepare source code for verification on Etherscan:
![etherscan-auction-utils-prepare](./etherscan-auction-utils-prepare.png)

Verification on Etherscan passed:
![etherscan-auction-utils-verification-passed](./etherscan-auction-utils-verification-passed.png)

Verified contract:
![etherscan-auction-utils-contract](./etherscan-auction-utils-contract.png)


## Add Auctin Utils address to Auction
Now we add Auction Utils address to Auction. Agent have direct access with CREATOR_ROLE on Foundation' Auction, proposal for Agent's action will be created in Congress only.
Command template (cast tx):
```
npx dao act <agentCongress> <auctionApplicationAddress> "addUtils(address)" <auctionUtilsAddress> --environment aragon:rinkeby
```
```
npx dao act 0x2d0970da60f36be926f78fbe8d6ef035eeaaf09f 0xc84b50db399445a488450b2f4c6a15dd6b05eae3 "addUtils(address)" 0xee2eb45b07CdcD488933d1614119434AB3db78e7 --environment aragon:rinkeby
```
Output:
```
  ⠸ Executing execute on 0x9cE69C31F98827eE5B8602988E07F8A0Af787035
  ✔ Executing execute on 0x9cE69C31F98827eE5B8602988E07F8A0Af787035

✔ Successfully executed: "Execute desired action as a token holder"
```

Proposal to add Auction Utils contract address to Foundation's Auction on Congress DAO:
![congress-auction-utils-proposal](./congress-auction-utils-proposal.png)

Passed droposal to add Auction Utils contract address to Foundation's Auction on Congress DAO:
![congress-auction-utils-proposal-passed](./congress-auction-utils-proposal-passed.png)

## 7. Distribute tokens

Now Congress may finish distribution process and transfer tokens to invertors and investors of Cyber. The remaining amount of tokens - is Congress share following distribution and Cyber whitepaper.

Command template (cast tx):
```
npx dao act <agentCongress> <agentCongress> "transfer(address,address,uint256)" <tokenFoundationAddress> <accountTo> <amountTokens> --environment aragon:rinkeby
```

### Inventors
Command (cast tx):
```
npx dao act 0x2d0970da60f36be926f78fbe8d6ef035eeaaf09f 0x2d0970da60f36be926f78fbe8d6ef035eeaaf09f "transfer(address,address,uint256)" 0x3638309F7fbBF5AF5F016Eb48e3Dbfa43C44d9cA 0xfBb2A29DFf113D518893C00387F6D5492898354e 10000000000000  --environment aragon:rinkeby
```
Output:
```
  ⠧ Executing execute on 0x9cE69C31F98827eE5B8602988E07F8A0Af787035
  ✔ Executing execute on 0x9cE69C31F98827eE5B8602988E07F8A0Af787035

✔ Successfully executed: "Execute desired action as a token holder"
```
Command (cast tx):
```
npx dao act 0x2d0970da60f36be926f78fbe8d6ef035eeaaf09f 0x2d0970da60f36be926f78fbe8d6ef035eeaaf09f "transfer(address,address,uint256)" 0x3638309F7fbBF5AF5F016Eb48e3Dbfa43C44d9cA 0xbb923dDA9DD20770B96Bc0231eE8631470e36402 10000000000000  --environment aragon:rinkeby
```
Output:
```
  ⠧ Executing execute on 0x9cE69C31F98827eE5B8602988E07F8A0Af787035
  ✔ Executing execute on 0x9cE69C31F98827eE5B8602988E07F8A0Af787035

✔ Successfully executed: "Execute desired action as a token holder"
```

Congressman votes on transfer proposals in Congress DAO:
Congressmans votes for transfers:
![congress-investors-transfer-1](./congress-investors-transfer-1.png)
![congress-investors-transfer-2](./congress-investors-transfer-2.png)

Foundation DAO, inventors got their tokens:
![foundation-investors-transferred](./foundation-investors-transferred.png)

### Investors
```
TO DO in mainnet
```

## Final state

### Foundation DAO
1. Foundation deployed by cyber~Congress Aragon DAO via Agent.
2. Foundation Aragon DAO name is cyberfoundation
3. Supply of TGOL24 is 700000000000000
4. Installed and prepared Auction Aragon application developed by cyber~Congress
5. Installed and prepared Vesting Aragon application developed by cyber~Congress
6. Permission's mananger on issue tokens on Token Manager set to Foundation's Voting application
6. Permission's mananger on assign tokens on Token Manager set to Foundation's Voting application

### Vesting application
1. Vesting end set to Thursday, February 20, 2020 12:00:00 AM GMT (1582156800)
2. Vesting application PAUSE_ROLE set to Congress Agent
3. Vesting application PROOF_ROLE set to Congress Vesting Companion Proofer external address
4. Vesting app have permission to issue tokens on Foundation's Token Manager
5. Vesting app have permission to assign tokens on Foundation's Token Manager
6. Vesting app have permission to burn tokens on Foundation's Token Manager

### Auction application
1. Auction zero round (Ethereum's Game of Thrones) set to 1581355800 Monday, February 10, 2020 5:30:00 PM
2. Auction rounds starts in 1581692400 Friday, February 14, 2020 3:00:00 PM GMT+0
3. Anount of rounds 20+1(zero window)
4. Duration of round is 23 hours + 1 sec
5. Collector of funds is Foundation DAO (Foundation's Agent address)
6. Vesting app have permission to burn tokens on Foundation's Token Manager

### Total transactions
Around 100

### Total burned gas
Around __

## Final thoughts
1. Aragon is an amazing project with endless features for DAOs creation and their operations.
2. Use your own node, not infura. It's boosts process 10x.
3. Prepare yourself for this process. Take a rest before. You should be fully concetrated because there is no room for error
4. TODO

## TODO in next doc iteration
0. Add more poetry and lyrics
1. Fix typings and styling
2. Fix images namings
3. Add more images
4. Add transactions with links to Etherscan
5. Verify Auction/Vesting applications on Etherscan (non trivial thing)
6. Add total transactions amount to Final State
7. Add total burned gas amount to Final State
8. Split service account (now one of Congressmans/Foundation's service account/Vesting's Proofer/Vesting Companion is one address)
9. Grant to prepared set of address PROOF_ROLE (now only one address - hot wallet, risk on compromentation and cannot be changed as permission manager set to Voting application and there is now shares in Congress to apply this action)
10. Think about set permission manager on PROOF_ROLE to Congress Agent (will allow Congress change proofers for vestings on Vesting application)
11. Split CREATOR_ROLE on Auction to CREATOR_ROLE and BURN_ROLE (now Congres Agent as Creator have access to both actions)
12. Provide upgrade procedure/crisis protocol for Auction and Vesting application in any emergency case (?) (this will need to grant Congress' Agent permission to manage application on Kernla of Foundation DAO which may be needed but gives Congress more power on Foundation)
13. Use Frame for CLI and all operations
