# Distributed Message Patters

Somtimes it's easy to think about how humans deal with messages in the real world.

### Message driven architecture
- Reactive systems put emphasis on Async, non-blockig messages
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
 
