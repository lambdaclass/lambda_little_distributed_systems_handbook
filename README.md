# Lambda little distributed systems handbook
This a work in progress. Many things are wrong, we are correcting them.
    
## Section A.1
### A.1.1 - What is Mathematics? And Computing?
From the shape of trees to the way ants cooperate and how stars position themselves in the sky, the world is full of patterns. Luckily we've evolved over thousands of years recognizing them and surely we got better at it.
Planes, telecommunication networks, bridges and almost everything in the present world wouldn't exist if we hadn't been able to understand patterns.
However, in order to harness their power we need to be able to talk about them. That's where **mathematics** come. We can think of maths as the language we use to describe patterns we perceive in the world. But how do we use…it? 

We can simplify the idea of maths as specifying which things are the same (equivalent) and then look at what we can do with that. Of course the procedure that makes the wheel spin is the **mathematical proof**, which is a formal way of proving a statement. For example:

> (The statement says that if a set of elements has a zero/null element, then it is unique)
> **Prove**: If $x, y, z \in X$
> 1. $0 \in X$ is an element such that $0 + x = x$  $\forall x \in X$ 
> 2. $x + y = y + x$ 
> 
> 3. then if $\exists 0' \in X$ such that $0'+x = 0$ $\forall x \in X$ it follows that $0 = 0'$
>
> **Proof**:
> 1. let $0$ and $0'$ be the ones from point 1 and 3 respectively.
> 2. we have that $0 + 0' = 0$ because of 1.
> 3. we also know that $0 + 0' = 0' + 0$ because of 2.
> 4. now $0' + 0 = 0'$ because of 3.
> 5. putting everything together, $$0 =_{(1.)} = 0 + 0' =_{(3.)} 0' + 0 =_{(4.)} 0'$$

Maths have been around for a long time. On the other hand, Computing, a branch of Mathematics has been around us for a shorter period.

![](https://i.imgur.com/N2tjijD.jpg)

Many people may have a mental image of computing such as _C_, _Javascript_, _Python_, _OCaml_ or maybe even _Erlang_, and some may think of _Rust_. That's incorrect. They are programming languages, at most. "Well sure, I didn't mean the languages but the machine running the code written in those languages". You have a point there, but we'd like to present a quite different definition.

Curry-Howard correspondence (the [cs3110](https://cs3110.github.io/textbook/chapters/adv/curry-howard.html) course site is a great place to learn more about it) is a correspondence proven which closes the gap between the world of computation and mathematics. It's a bit too much for the purpose of this book, but in short, it shows that computation is nothing but receiving proofs and simplifying them (and things such as function definitions also correspond to prepositions and proofs).
### A.1.2 - What is a distributed system?
### A.1.3 - Distributed System's problems
### A.1.4 - FLP Impossibility
The consensus problem involves an asynchronous system of processes, some of which may be unreliable. The problem is for the reliable processes to agree on a binary value. [It was shown](https://groups.csail.mit.edu/tds/papers/Lynch/jacm85.pdf) that every protocol for this problem has the possibility of non determination, even with only one faulty process.
Real systems are subjects to a number of possible faults, such as process crashes, network partitioning, and lost, distorted, or duplicated messages. One can even consider more Byzantine types of failures.
No completely asynchronous consensus protocol can tolerate even a single unannounced process death. This important problem has no robust solution without further assumptions about the computing environment or still greater restrictions on the kind of failures to be tolerated!

In a nutshell, the FLP impossibility states we can’t have safety, liveness, full asynchrony, and fault tolerance all at the same time.

### A.1.5 - CAP theorem
### A.1.6 - Recap

## Section A.2
### A.2.1 - Gossip Protocols / Distribution
Now that we introduce the notion of distributed system, we need to understand how the different participants of the system communicate between them. They'll need to send messages and status updates to keep all the other nodes updated and aware of the latest state of the network.

Imagine we have a number of nodes that are members of a network or system. One node wants to pass a message or a request to all the other nodes in the network, well the node would have to go node by node communicating the message and here we encounter the first communication problem. Think of a network with 1000 nodes, if all of them at some point want to deliver a message to all the rest, they have to know every other participant node and the delay between the first message and the last one it would be huge, leading to inconsistencies in the state of the network and latency. On the other hand we expect to have unexpected disconnections from some nodes but we want the information to keep being distributed and not to stop for some node failure.

Gossip protocols try to solve all these problems and make a better and faster communication between nodes. They are basically broadcast protocols between all the peers. The idea behind the gossip protocols is based on the fact ,like in epidemic theory, that starting with a single message the time that it would take to give the message to all the population is proportional to the log of the population size, or the number of nodes in the network in this case. There are numerous variants of the Gossip protocol that can be applied to different scenarios depending on the needs.

All the gossip protocols follows mostly the same steps, where a node choose $n$ random nodes and deliver the message. Then every node do the same until every node received the message. For this reason the algorithm is not deterministic and there is no secure way to know if all the nodes actually received the message correctly.

### A.2.2 - What is Consistency?
When we use the term consistency we refer to have a consistent behaviour. 

In distributed systems is expected for every node to has the same info about specific data at a given point in time independent of whichever client has updated that data. If a request is made to a node, we want to get the same response. From the outside it should be like there is only one node doing all the work and giving always the last updated data (or an error). 

There is two types of consistency models that depends on what model we prefer of the mentioned in the CAP theorem section.
The first one is known as **strong consistency**. It ensures that all the nodes have the latest value of data at the same time, to achieve this the nodes may be locked until the data is properly updated. This model favors consistency over availability and partition  tolerance.
The **Eventual consistency** is another consistency model that ensures that at some point in time the nodes will have the latest data but it may not be at the exact time as the change was requested. This favors data availability losing some consistency in the way.

### A.2.3 - What is Consensus?
Consensus in general can be defined as an agreement between some known agents on their actual state using some type of interaction between them. 

We can use this concept in the context of blockchain. We can think of the consensus as a procedure. The goal of this procedure is to reach an agreement on the actual state of the network that involves peers that communicate between them but are independent. 

This task seems easy if we think on a centralized network where we have one trusted peer acting as a coordinator for the rest that communicates and receives state updates and then sends this new update to the rest of the nodes. Since blockchain is a decentralized network we have to think a little bit harder how we can reach consensus and the appropriate way for the network members to communicate.

#### A.2.3.1 - Consensus Algorithm

Assume a collection of processes that can propose values. A consensus algorithm ensures that a single one among the proposed values is chosen. If no value is proposed, then no value should be chosen. If a value has been chosen, then processes should be able to learn the chosen value. The safety requirements for consensus are:
- Only a value that has been proposed may be chosen.
- Only a single value is chosen, and
- A process never learns that a value has been chosen unless it actually has been.
The goal of liveness is to ensure that some proposed value i eventually chosen and, if a value has been chosen, then a process can eventually learn the value.

### A.2.4 - The Byzantine Generals Problem
The Byzantine Generals Problem refers to the difficulties that decentralized systems have to reach consensus or agreement in the decision-making of some specific action. The difficult part comes because the party doesn't expect a central member who knows all the participants and sends and receives all the communications between them.

The game theory analogy behind the _Byzantine Generals Problem_ talks about several generals trying to invade a city but their only way to communicate is message passing but there is no secure way to do that. The message could be lost in the middle of the way, or be intercepted, a general could be a malicious one and start sabotaging all the communication, and how a general knows that another general received the message? all this kind of problems leads to a disaster in synchrony. 

Related to the computer field imagine all these generals being peers in a network and all these problems being unexpected disconnection, malicious participants, etc. and instead of attacking a city the participants want to reach a consensus about something. The idea of the network is to be tolerant of this kind of problem.

### A.2.5 - Paxos
Paxos is an algorithm that enables a distributed set of computers to achieve consensus over an asynchronous network. To achieve agreement, one or more of the computers proposes a value to Paxos. Consensus is achieved when a majority of the computers running Paxos agrees on one of the proposed values.

<!-- Possible introduction to consensus algorithms
Assume a collection of processes that can propose values. A consensus algorithm ensures that a single one among the proposed values is chosen. If no value is proposed, then no value should be chosen. If a value has been chosen, then processes should be able to learn the chosen value. The safety requirements for consensus are:
- Only a value that has been proposed may be chosen.
- Only a single value is chosen, and
- A process never learns that a value has been chosen unless it actually has been.
The goal of liveness is to ensure that some propsoed value i eventuallly choen and, if a vaue has been chosen, then a process can eventually learn the value.

This roles are performed by three classes of agents in Paxos:
-->

Paxos has three classes of agents: proposers, acceptors, and learners. Proposers receive requests (values) from clients and send a proposed value to a set of acceptors. An acceptor may accept the proposed value. The value is chosen when a single proposal with that value has been accepted by the majority of acceptors. A response from an acceptor represents a vote for a particular proposal. Learners announce the outcome.

After choosing a leader which plays the role of a distinguished proposer, the algorithm operates in the following two phases:
1. Prepare a request
    a. A proposer selects a proposal number $n$ and sends a $prepare$ request with the number $n$ to a majority of acceptors.
    b. If an acceptor receives a $prepare$ request with a number $n$ greater than that of any $prepare$ request to which it has already responded, then it responds to the request with a promise not to accept any more proposals numbered less than $n$ and with the highest-numbered proposal (if any) that is has accepted.
2. Accept request
    a. If the proposer receives a response to its $preprare$ requests (numbered $n$) from  a majority of acceptors, then it sends an $accept$ request to each of those acceptors or a proposal numbered $n$ with a value $v$, where $v$ is the value of the highest-numbered proposal among the responses, or is any value if the responses reported no proposals.
    b. If an acceptor receives an $accept$ request for a proposal numbered $n$, it accepts the proposal unless it has already responded to a $preprare$ request having a number greater than $n$.

### A.2.6 - Raft
Raft is a consensus algorithm that is designed to be easy to understand. It's equivalent to Paxos in fault-tolerance and performance. The difference is that it's decomposed into relatively independent sub problems, and it cleanly addresses all major pieces needed for practical systems.

#### A.2.6.1 - High Level Overview
A node can be in one of three states, the *Follower* state, the *Candidate* state or the *Leader* state.
All nodes start in the follower state. If followers don't hear from a leader then they can become a candidate. The candidate then requests votes from other nodes. Nodes will reply with their vote. The candidate becomes the leader if it gets votes from a majority of nodes. This process is called *Leader Election*. 
All changes to the system now go through the leader. Each change is added as an entry in the node's log. To commit an entry the node first replicates it to the follower nodes, then the leader waits until a majority of nodes have written the entry. The leader then notifies the followers that the entry is committed. The cluster has now come to consensus about the system state. This process is called *Log Replication*.

#### A.2.6.2 Leader Election
In Raft there are two timeout settings which control elections. First is the *election timeout*. The election timeout is the amount of time a follower waits until becoming a candidate. The election timeout is randomized to be between 150ms and 300ms. After the election timeout the follower becomes a candidate and starts a new *election term*, votes for itself and sends out *Request Vote* messages to other nodes. If the receiving node hasn't voted yet in this term then it votes for the candidate and the node resets its election timeout. Once a candidate has a majority of votes it becomes a leader. The leader begins sending out *Append Entries* messages to its followers. These messages are sent in intervals specified by the *heartbeat timeout*. Followers then respond to each *Append Entries* message. This election term will continue until a follower stops receiving heartbeats and becomes a candidate. Requiring a majority of votes guarantees that only one leader can be elected per term. If two nodes become candidates at the same time then a split vote can occur. Let's make an example of this situation. Suppose a four node network, two nodes both start an election for the same term and each reaches a single follower node before the other. Now each candidate has 2 votes and can receive no more for this term. The nodes will wait for a new election and try again until one node receives a majority of votes and becomes leader.

#### A.2.6.3 - Log Replication
Once we have a leader elected we need to replicate all changes to our system to all nodes. This is done by using the same *Append Entries* message that was used for heartbeats. Let's walk thought the process. First a client sends a change to the leader. The change is appended to the leader's log, then the change is sent to the followers on the next heartbeat. An entry is committed once a majority of followers acknowledge it and a response is sent to the client. Raft can even stay consistent in the face of network partitions. Let's make an example of this situation. Let's say we have five nodes, three in one partitions and two in another. Because of our partition we now have two leader in different terms. If we add a client that sends a request to the two-node partition the leader of that partition cannot replicate to a majority so its log entry stays uncommitted. Another client sends a request to the other partition. This will succeed because it can replicate to a majority. If the partition is healed, then the two-node network will roll back their uncommitted entries and match the new leader's log. Now the log is consistent across the cluster.

### A.2.7 - (This section may not be included in the future) Distributed Systems Specification (Tla+, temporal logic and more)
### A.2.8 - Replication Models, Master/Follower and sharding
### A.2.9 - CRDTs
### Section Recap

## Section B
### B.1 - Problems within the current financial system
### B.2 - A non globalized world
### Section Recap

## Section C
### C.1 - What is a Blockchain?
A blockchain is a distributed database or ledger that is shared among the nodes of a computer network. As the name suggests, it is basically a chain of blocks so every block inside this ledger has information about the previous block creating a _chain_.

Decentralized blockchains are immutable, which means that the data entered is irreversible, this is not entirely true for all implementations nowadays, and later we are going to explain this specific usages of the blockchain to explain that topic better.
The blockchain may be decentralized so no single person or group has control over the content of the blockchain, rather, all users collectively retain control.

The blocks are created and added to the chain by the participants of the network. This chain keeps an order on all its blocks, so we want to avoid duplicated blocks that could split the main chain indefinitely and prevent that some malicious node on the network add a block to its own benefit that is not correct for the rest of the network.

Once a node has recorded a decision, a copy is sent to all other nodes in the network, making the information redundant. 

### C.2 - Blockchain's approach
The Byzantine generals problem can be solved with the help of a blockchain. Basically giving the participants a way to communicate safely and being tolerant against byzantine failures at the same time.

Each individual participating on the blockchain with intentions of building it is like a new general. A blockchain creates a layer that can be trusted without needing to trust every other individual. This is accomplished by a network of nodes coming together to agree on the truth before it is recorded. If the general is insecure about a decision or think that a communication it's not trustworthy, the other generals can verify it using what they know to be true.

To maintain the security and immutability of the data that lives in the blockchain, cryptography was required and its one of the main tools that blockchains use to keep everything safe.

In terms of the CAP theorem:
A blockchain achieves consistency when forks are avoided. This property is referred as **consensus finality** that we are gonna talk about later.
A blockchain is available if transactions submitted by clients are served and eventually committed.
For Partition Tolerance when a network partition occurs, authorities are divided into various groups in such a way that nodes in different groups cannot communicate each other.

### C.3 - Did Bitcoin solve BGP?

With regard to money, Bitcoin needs to handle ownership and avoid double-spending (UTXO!) as a monetary system. Bitcoin employs a blockchain, this public distributed ledger, that saves the history of all transactions to accomplish this in a trustless way. The blockchain for Bitcoin, in the Byzantine generals analogy, is the decision that all parties must agree on.

If all the nodes in the Bitcoin network could agree on which transactions happened when and in what order, they could verify Bitcoin ownership and create a working, trustless monetary system without the need for a centralized authority.

Bitcoin was the first real solution to the making of decentralized currency using blockchain technology and solving the Byzantine generals problem. The world "solving" here is important, Bitcoin actually solves the problems that we mentioned but to achieve that it uses some other incentives and mechanisms and not real consensus between the participants.

Bitcoin avoids dealing with consensus to see which block is the next and who is gonna propose it. It uses a mechanism called _Proof-of-Work_. Actors in Bitcoin called miners are designed to validate blocks and they compete with other miners to solve cryptographic puzzles to produce blocks and add them to the blockchain. By employing a proof-of-work mechanism, Bitcoin overcame the Byzantine generals problem because it doesn't need consensus at all. To add information to the blockchain (blocks) a network member must demonstrate that they put a lot of effort into making the block. This work comes at a high cost to the creator, this incentivize them to share correct information, even making the generation of wrong information unprofitable.

Thus this mechanism seems great trying to mitigate the byzantine faults, it carries other problems that we have to care about now. The first one is that is highly inefficient in terms of energetic resources. The mechanism itself incentivize the participants to spend money on resources to maximize the "power" and maximize the probability to be the one who solves the next cryptographic puzzle. 

On the other hand, we talked about how the things that are uploaded on the blockchain are immutable and how this is not entirely true. The thing is that when a new block is added to the blockchain it relays on probabilistic finality so the irreversibility of a new transaction is not deterministic. There's a point where the probability for one block to be reverted is close to 0 so we can assume that is immutable, this point is considered when we have 6 consecutive blocks, so the problem is that a transaction takes longer to be confirmed and assured as definitive, and as I said is always a probability.

### C.4 - Proof of Stake

Proof-of-stake born as an alternative to Proof-of-work mechanism. Ethereum launched a Proof of Stake chain the 1st of December 2020. This chain uses another consensus algorithm different from the Ethereum Proof-of-Work chain or Bitcoin consensus algorithm.

Instead of using power to solve a puzzle and create a demonstration that you are worth adding the next block to the blockchain, proof-of-stake, generates a general stake where all validators (participants that have intentions of proposing the next block) have to put tokens to prove that they are worthy of proposing the next block, the one who "stake" the greatest number of tokens is the one with the greatest probability to be chosen to add the new block to the blockchain.

Now imagine the scenario where a validator, representing one of the "generals", happens to be malicious and wants to include fake transactions or alter the transactions in the block he has to build, then he’ll risk losing all the coins that he staked. The idea of the proof-of-stake is that you earn a small incentive from the transaction fees if you are truthful, but lose everything if you are dishonest.

Basically the way proof-of-stake tries to solve the BGP is by saying to all the nodes that want to be the next designated validator to lock their tokens and punish them if they behave badly. So again it's not about reaching a full consensus between the participants to solve the byzantine faults its about forcing the decision with, in this case, economic incentives.

This approach attacks the last problems that I talked about in the previous section. First, the participants don't need to spend a lot of resources because they don't need to compete between them to be the first solver of the puzzle. Also, the finality and the probability for a transaction to be considered immutable in the blockchain takes considerably less time compared to proof-of-work.

### C.5 - Proof of Work vs Proof of Authority

Proof-of-Authority comes as another alternative after proof-of-work and proof-of-stake, introduced as more energy-efficient with fewer resources needed. The base of the proof-of-authority mechanism lies in the fact that all the sets of validators that can be elected for building and adding the next block are previously approved for another entity to avoid malicious validators in the future. Normally, these sets of validators are small compared to those that participate in proof-of-work or proof-of-stake based blockchains. 

This also carries another discussion and it's the level of decentralization of a blockchain that uses proof-of-authority. We've been talking about the byzantine failures and the intentions of these mechanisms to mitigate those problems maintaining a decentralized and trustless blockchain. With mechanisms like this one the permissioned blockchains entered the game.

Proof-of-work doesn't need any pre-approval of miners because it supposes that everyone starts with the same opportunities to create and add the following block to the blockchain, we know that it's not true but, for Bitcoin, there is no necessity to have a set of allowed participants so the incentive to go and try to be the one who solves the puzzle is bigger. In Proof-of-authority entering this validator set can be hard so it could be the first barrier for newcomers to the network.

On the other hand the transactions per second that can be executed with proof-of-authority is much bigger and it needs less computational resources to do it.

### C.6 - What is Money
### Section Recap

---

# SOURCES

- introduction
    - [A history of Computation, Logic and Algebra](https://pron.github.io/posts/computation-logic-algebra-pt3)
    - [What is consensus](https://ethereum.org/en/developers/docs/consensus-mechanisms/#what-is-consensus)
	- [What is a consensus mechanism](https://ethereum.org/en/developers/docs/consensus-mechanisms/#what-is-a-consensus-mechanism)
	- Why is important for blockchains
	- Algorithms vs node permission mechanisms
	- Examples

- Preliminary on Distributed Systems
	- [The Byzantine Generals Problem](https://dl.acm.org/doi/pdf/10.1145/357172.357176)
    - [Epidemic Algorithms for Replicated Database Maintenance](http://bitsavers.trailing-edge.com/pdf/xerox/parc/techReports/CSL-89-1_Epidemic_Algorithms_for_Replicated_Database_Maintenance.pdf)
	- [What it means to be Byzantine Fault Tolerance (BFT)](https://arxiv.org/pdf/1803.05069.pdf) (HotStuff introduction)
	- Safety and liveness
	- [FLP impossibility](https://groups.csail.mit.edu/tds/papers/Lynch/jacm85.pdf)
	- Partially Synchronous, Synchronous, and Asynchronous Communication ??????

- Proof of Stake
	- [What is Proof of Stake?](https://ethereum.org/en/developers/docs/consensus-mechanisms/pos/#what-is-pos)

- Formal Specifications / TLA+
    - [TLA+ in Practice and Theory](https://pron.github.io/posts/tlaplus_part1)

- Algorithms
	- [State Machine Replication approach](https://www.cs.cornell.edu/fbs/publications/SMSurvey.pdf)
	    - DLS
		- [PBFT](https://pmg.csail.mit.edu/papers/osdi99.pdf)
		- [Tendermint](https://tendermint.com/static/docs/tendermint.pdf)?
		- [Algorand](https://arxiv.org/pdf/1607.01341.pdf)
		- DiemBFT
		- [Casper](https://arxiv.org/pdf/1710.09437.pdf)
