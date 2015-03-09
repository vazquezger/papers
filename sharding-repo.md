# Sharding a data repository to allow concurrent reading components

## Design Problem
- Components read data from a single, shared data repository
- Components transform/"produce" new data and pushes it to a queue to be pulled by a cluster of consumers
- Data records in the shared repo must be processed exclusively "by one and only one producer"

### Things we want to have
- Multiple components read data from the repo so to allow concurrency and avoid SPOF
- Producers do not synch each other for access to the shared repo so to avoid locks issues

### Things we want to avoid
- Avoid producers contention while reading the repo
- Producers synch each other so that only one read the repo
- Synch impacts performance negatively (only one prod at any time)
- Potential deadlocks (a producer not releasing the lock)

## Solution Alternative to Synch-ed Producers
- Shard the repo based on a hash key so that each producer works on one and only one shard of data
(avoiding contention)
- There's one token in a queue per shard in the repository
- Producers read the tokens from the queue to acquire exclusion over a shard
- Once a prodicer is done with the shard it release the token and put it back to the queue
- There's no hard assignment between producer and shards (any producer may work on any shard at a given
point in time)
- No dependency between number of producers and number of shards
 - Tokens contain metadata that describes data shards
 
_Figure 1_ Conceptual architecture of the solution showing the behavior of a producer (see components description
below)

### Tokenizer Component
There's a tokenizer component that decides the number of shards in the repo.
The tokenizer runs periodically (every hour, or so) and:
- Computes the number of tokens based on the data in the repository. See "(0)" in Figure 1
- Pushes the tokens into the tokens queue (See (0) in the diagram above)
- Eventually, removes the tokens from the queue to stop producers work
- Checks that all tokens are there in the "tokens queue" (otherwise a producer may have crashed while
retaining a token)

Tokens are consumed by producers to know which shard they shall work on.

A token holds metadata that's used by producers to work with a shard:
- "Shard-Id": an integer from 0 to Number-Of-Shards - 1
- "Number-Of-Shards": The total number of Shards
- "Hashed-Field": the field in the mongo record that's in the hash function
- "Hash-Function": decides if a record belongs to the shard

For instance, for a shard size equals to 10, a record belongs to my shard if: "record.JobId' MOD 10 == Shard-Id".

### Producer Components
Producers do not cycle, they run one cycle and terminate (meaning that there is no "while (true) do".
A producer executes the following steps within one cycle:
- (2) The producer reads a token from the "tokens queue". See "(1)" in the Figure 1.
- (3) The producer reads records from the shard that satisfy the token's hash function
- (4) The producer pushes the data into the "prod/cons queue"
- (5) The producer pushes the token back to the "tokens queue" (so that the token is re-usable by other producers, or the same producer

### The Tokens Queue
The "tokens queue" is actually a data structure that implements a list of slots where each slot holds a token.

The queue might be a regular queue, or might be implemented with the push/porp structure of REDIS or, even more
reliable, itt can be implemented using data structure in mongodb that implements a list of slots where producers
content for slots and aquire one slot via an optimistic locking mechanisms (through a "version numbers" strategy).

### Design Pros
- Multiple producers run concurrently
- There's no contention between producers; no lock mechanisms
- Any arbitrary number of producers: No SPOF

### Design Cons
- More operational work due to a new queue
- More complex design
- Tokenizer component shall be monitored, though it's not vital to keep the system running
- Can a "mod" function be executed in a mongo query ????

