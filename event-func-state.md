
# EFSO: event, function, state, order

A computer system:
- ingests inbound events from external systems
- defines a selected set of fuctions on these events
- computes these functions on demand

Fuction results are available for on-demand polling,
or as outbound events pushed to external systems.

## function

Some functions are defined by composing other functions.

A function is an aggregate over events.
(typically if it reduces cardinality)

Functions can be composed with other functions,
forming a DAG of aggregates.

Fuctions form the ontology of the system,

Some of functions are recognized as base entities in the ontology,
which are not composed from other functions.
A lot more other functions are derivitive; they could be mistaken
as entities of their own right if computed physically and result persisted,
result not entirely depend on events, also on environment at compute time;
they have extra inputs other than external events.

Functions are not physical; definition, not computation.

worth to stress on *definition* of function, which is often overlooked in engineering.

compare function results: same result: some equivalency criteria; weaker than identical.

### computation

physical, space time.

### "action"

just functions? FP-nuts? what about actions?

internal action: all are devices for the purpose of compute functions.

external action: results of some functions. outbound events delivered by 
some device. such deliveries are interesting inbound events to this system itself.

## woes of function:

most functions require a structure some events, not just set of events.
The strucutrure is mostly a sequence of events.
The ordering needs to be established/provided by function callers.

eventual consistency, i.e. temporary inconsistency. 
it's a compromise, not a feature. users are conditioned to accept it.
eventual consistency at infinite future: it's never consistent.
but maybe consistency is not definable: such as world population at T.
better example: number of active users of a mobile app at time T.
betetr example: 2 partitions each with instant consistency; sum of them.

Sometimes instant consistency makes no sense even if it's definable/computable.
world population at T: you can define it and compute it, but that's just your choice.
asymptotic consistency? average consistency? 
but nothing wrong if your computer system implements an insant consistency,
as long as it's admitted that it is incidental, not some absolute truth.

it's costly to compute fuctions by-the-letter; 
at mininum it needs to read all events.

## state machine

Opposed to Church's function is Turing's machine.
Many functions can be expressed as

    f(En) = f'(s_n), s_n+1 = t(s_n, e_n)

where s_n and t require only O(1) space and time.

This is a _finite_ state machine; we are never bothered 
by that in practice -- there is not real Turing machine,
all computer systems are FSM.

but, it *is* deterministic. randomness will be discussed later.

physical state machine in action.
not to invent another word, SM typically referes to physical one.

The current state is available for quicky retrieval.
State transitions are typically inexpensive to compute.
They are real-time incremental/accumalative pre-aggregations.

There are solutions (such as auto-refresh materialized view) 
that automatically convert and compute a class of functions as state machines.

A physical state machine in action imposes an order on input events.
State transitions can become outbound events fed to other systems.

A physical state machine can ACK events; however, lack of ACK for an event
is possible and leaves event sender in wonder. Often it's solved by
repeatedly re-sending the events. A state machine thus needs to be
prepared to deduplicate events, i.e. idempotence.

physical state machine is not pure -- results, i.e. ordering,
depend on circumstances at space time where it occurs.
event replay may end up with different results.

## woes of state machine

## function vs state machine

When we specify a system, which one should we use,
function or state machine? Perhaps both.

Typically, our first intuitition of a system is a set of vaguely
specified state machines, with some core entity states
and main state transition.

Often we rush to implement the system based this vague understanding, 
amending state attributes and transitions as we go along.

That is because the full spec of state machines is difficult to establish,
due to huge combinations of states and events.
Even if there is a complete spec, it's difficult to assert that it does
the right thing, if the "right thing" wasn't specified ahead of time.

On the other hand,
A function is precisely defined over any possible sequence of events.
forced to be precise. 
Not a trivial task either, but perhaps easier.

"eventual consistency" is easier to understand in terms of functions.

If it is acceptible that a function is computed by-the-letter on-demand,
we should leave it that way. No state machine needed.
Or use auto func->state solutions without manual state machine spec/impl.

If a function has to be converted to a state machine for performance,
the state machine has the function as the target/goal.
It guides us to design the state machine completely and correctly,
and the correctness can be checked opposed to the function.

## databases

transactional: state machine. serialize transactions, i.e. order events.

query: function: filter individual states; agg; composition of functions.

## event order

While a state machine orders its input events,
this order is typically not persisted to be available for other uses.
Different state machines could order events inconsistently.

We want an event ordering that is consistent to all functions
and state machines.
Replay for re-compute, or for new functions.

if the order of e_i, e_j doesn't change the result of any function,
they don't need to be ordered.
partial order. partition. (selected set of functions in current considerations)
for partition/performance. 

## "eventual consistency"

move discussions here.

## event sorter

event sorter. state machine does nothing but order input events. (partial DAG)
events are persisted along with the order.

event feeder may get positive ACK from event sorter.
However, there is no reliable way to know event is dropped by the sorter.
Either sorter implements idempotence in event ingestion, detect/ignore dupe events,
or downstreams should be prepared for duplicate events.

event filter. based on event attributes. time range, open-ended.
delivery in order. 
(some topological order of the partial order DAG. )

read consistency:
- r sees ej => r sees any ei that ei<ej
- r1 sees e => r2 sees e. (same observer)

event sorter is the only physical device in the system; 
everything else in the system are to compute _pure_ functions,
functions over ordered events and nothing else.
event sorter is critical and cannot be down.

ideally, replayable - all destroyed except event sorter. 
system can be reconstructed by replaying events from start.
maybe too difficult to achieve completely; at least for some part.

good to aim for "pure". 
but maybe too difficult to be pure on everything; compromise.

### randomness?

not deterministic on events. how to deal with it if we want to be replayable:

timestamp/machine-ip of a computation step -- should not be essential.

UUID? only need because contention from concurrent events.
not needed if events are "globally" ordered.
(perhaps not needed either just to avoid contention from partitions,
why not just prefix id with partition key)
but if an external system do demand UUID...

randomness mandated by application requirements, external requirements,
contention from partitions, or some algo used? 
think about a game with a dice.
insert random seeds to event sorter?
true random? throw dice -> outbound event to god who plays dice, 
sends result back as inbound event. 
SM: state=requesting random, waiting on random.
transition action: create random, sent to event sorter.
indeed this introduces latency.

pause/delay: post an article; blocked for 10s. rate limiting?
during replay how to skip them fast.

## woes of partial order

is it really cheap because it doesn't need to order all events?
complicated. limited to preconceived boundary of features.
overly unnecessary ordering, hard to re-partition to scale.
adding new ordering to old events, without changing old orders.
example: indepedent accounts. trouble when accounts need to interact with each other.
Accidental ordering: two events are ordered when they don't need to be.
If a new function is defined that requires their ordering, 
the previously established order may not be correct.
Difficult to order (account, inventory).

system evolution means new events and new functions (possibly drop old/bad functions)

## global total order?

global total order among all events. (all functions, including future ones)
If this is possible, it removes a major headache in system design and implementation.

Sub-systems can still be partitioned to concurrently process subsets of events.
However when they join they may need to align on the global order.

Global order is indeed trivially achievable with one central device (such as kafka)
for many systems of moderate scale.
(which wasted a lot of resources to design for a scale they'll never each)
They probably should do global order other than get into the mess of partial order.

todo: one kafka one topic one partition: max throughput/latency? 
bottleneck? storage?

### open question: 

since we all pretend to be building infinitely scalable systems.

For large scale: an event sorter, for global total order, scalable, resilient, performant.

N partitions (each with M replicas) with local clocks per partition.
event ordered by (timestamp, partition)
clock sync frequently; still drift in clocks; 
more importantly, diff latency in serving oberservers.
Need to align on timestamps among partitions for total order.

