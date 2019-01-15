# Distributed Message Patters

Somtimes it's easy to think about how humans deal with messages in the real world.

## Message driven architecture
- Reactive systems put emphasis on **Async**, **non-blocking** messages
- Messages are sent without waiting for a response
- The sender may be interested in a response, but it comes async

### Advantages to Async/Non-Blocking
- Resources can be freed immediately
- Reduced contention (potential for higher scalability)
- Messages can be queued for deliever in case the reciever is offline (potential for higher reliability)

### Synchronous Messages
- Async should be used in reactive system
- Synchronous can be used, but requirements relaxed
  - Ack message synchronously but process it async
- The need for synchronous message should be driven by domain requirements
- Best Practice: Use async by default, fall back to sync when forced
  - Understand the consequences of this choice
 
### Cost of Async messages
- In a mono app, transactions are mostly used
- Microservices, distributed systems, and async all make transactions more difficult

## Sagas
- The saga pattern is a way of representing a long running transaction
- Mutliple requests are managed by a Saga
- Individual requests are run in sequence or parallel
- When all requests complete, the Saga is complete
- Often modeled as a finite state machine

### Failures in Sagas
- Each request is paired with a compensating action
- If any request fails, compestatin actions are executed for all completed steps
- The Saga is the completed with a failure
- If a compensating action fails, then it is retried. This requires idempotence

### Timeouts in a Distributed System
- In a distributed system, timeouts present a difficult problem
- A request timeout presenst us with 3 possible scenarios
  - The request failed
  - The request succeeded, but the reply failed
  - The request is still queued and may succeed of fail
- Because the request may have succeeded, we need to run the compensating action
- The reqeust may still be queued, therefore the compensating action may complete first
  - Compensating actios must be commutative
 
#### Compensating vs Roll Back
- They are different
- Rollback
  - Implies the transaction has not completed
  - Removed evidence of this transaction
- Compensation
  - Applied on top of a previously completed action
  - Evidence of the original action remains
 
### Moving forward with Failures
- Saga's apply compensating actions to undo changed mage during previous stages
- Al alternative approach is to move forward despite the failures
- A Saga is coupled to failures, but if we can move forward we break this coupling
  - Eg: 
    - Retries
    - Fallback to cached or default values
    - Move failures to an error queue and process them separately

### Saga pattern in Lightbend ecosystem
- Saga's are often implemented using Akka Actors
- Saga's can be represented as Finite State Machines
- Actors naturally model Finite State Machines which makes them natural choice
- Akka presistent FSM is a popular option
  
## Message Patterns

### Two Generals
- Illustrates the impossibility of reaching a consensus over a unreliable communication channel
- Two armies try to coordinate a syncronized attack
- Messengers travel through enemy territory and my be killed or captured
- The potential loss of messengers results in an infintei ack chain
- No matter how many acks sent, neither A or B can even be 100% sure the other is ready to attack
- Stong consistency is impossibly

### Deliver Guarantees
- Two generals problem shows that over a unreliable network we can not guarantee message receipt
- This means that **Exactly Once** delivery of messages is **impossible**
- Instead we must be satisified with either:
  - At Most Once
  - At Least Once
- Note: We can simulate **Exactly Once** delivery using **At Least Once** delivery and *idempotence*

#### At Most Once Delivery
- Promises that no message will ever be delivered more than once
- An effort is made to deliver the message, but if a failure occurs we never retry.
- No retry means:
  - Messages are never duplicated
  - Messages may be lost
- Requires no storage of messages
- Easy to implement

#### At Least Once Delivery
- Guarantess that all messages will **eventually** be deliverd
- When a failure occurs:
  - The message may not have been deliverd
  - The message my have been delived, but not acked
- Failure always result in a retry
  - Means messages may be delieved more than once, but they are never lost
- Required message be stored by the sender to enable retries

#### Exactly Once Delivery
- **Not Possible**
  - In the event of a network partition, or lost message, we can't guarantee wheathr out message was received
  - Failure requires resending the message which creates potential duplicates
- **Exactly Once Delivery** is simulated using
  - At least once delivery
  - Deduplication or Idempotence
  - Required storage on both the sender and the reciever
 
#### Delivery Guarentees in the Lightbend ecosystem
- Akka uses **At Most Once** delivery by default
- Akka persistence has an option for **At Least Once** delivery
- Lagom supports both through it message broker API
- Recomendation: **At Least Once** at edge of system, and **At Most Once** inside the system

### Message Patterns
- Each microservice can depend on other microservices, sending in a point to point fasion
- Or each microservice can leverage pub/sub message broker or bus to decouple

#### Point to Point
- Each service depends on other services directly
- Services are directly coupled to each others API
- Services know about, and understand their dependencies

#### Publish/Subscribe
- Services publish messages to a common message bus
- Other services subscribe to the messages
- The publisihing service has no knowledge of the subscribing service
- Subscribing services also have no knowledge of the publishing services
- Services are coupled to the message format, not to each other

#### Point to Point vs Pub/Sub
- Point to Point
  - Dependencies are more clear and distinct
  - Coupling is higher
  - Complexity is more directly observable
- Pub/Sub
  - Dependencies are more flexible
  - Coupling is lower
  - Complexity may be harder to see
