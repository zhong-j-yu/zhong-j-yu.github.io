
# event, function, state, and event

A system accepts inbound events from external systems,
defines and computes a selected set of fuctions on these events.
Fuction results are available for on-demand polling,
or as outbound events pushed to external systems.
(Act of push may be interesting inbound events to this system).
There are also internal events between subsystems.

## function

Fuctions form the ontology of the system,
some of which are recognized as base entities.

A function is an aggregation of events.

Functions can be defined on top of other functions,
forming a hierachy of aggregations.

## Problems with function:

most functions require a structure some events, not just set of events.
The strucutrure is mostly a sequence of events.
(eventual consistency, i.e. temporary inconsistency. 
it's a compromise, not a feature. users are conditioned to accept it)

it's costly to compute fuctions by-the-letter; 
every computation requires iterating through all events.

## state

Opposed to Church's function is Turing's state machine.
Many functions can be reasonally expressed as

  f(En) = f'(s_n)
  s_n+1 = t(s_n, e_n)

The current state is stored for quicky retrieval.
State transitions are typically inexpensive to compute.
They are real-time incremental/accumalative aggregations.

There are solutions (such as Risingwave Materialize) that automatically
convert and compute a large class of functions as state machines.

A state machine imposes an order on input events.
State transitions can become outbound events to other systems.

Transactional databases are state machines.

## function vs state

When we specify a system, which one should we use,
function or state? Perhaps both.

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

## event order

While a state machine orders its input events,
this order is typically not persisted to be available for other uses.
Different state machines could order events inconsistently.

We want an event ordering that is consistent to all functions
and state machines.
Replay for re-compute, or for new functions.

partial order. partition. (selected set of functions in current considerations)
for partition/performance. 

## event sorter

event sorter. state machine does nothing but order input events. (partial DAG)
events are persisted along with the order.

event filter. based on event attributes. time range, open-ended.
delivery in order. 
(some topological order of the partial order. )

read consistency:
- r sees ej => r sees any ei that ei<ej
- r1 sees e => r2 sees e. (same observer)


Kafka is an event sorter where events are partitioned by type and key.

## woes of partial order

is it really cheap because it doesn't need to order all events?
complicted. limited to preconceived boundary of features.
adding new ordering to old events, without changing old orders.
example: indepedent accounts. trouble when accounts need to interact with each other.
Accidental ordering: two events are ordered when they don't need to be.
If a new function is defined that requires their ordering, 
the previously established order may not be correct.

## global total order

global total order among all events. (all functions, including future ones)
If this is possible, it removes a major headache in system design and implementation.

Global order is indeed trivially achievable for many systems of moderate scale.
(which wasted a lot of resources to design for a scale they'll never each)

Sub-systems can still be partitioned to concurrently process subsets of events.
However when they join they may need to align on the global order.

## open question: 


For large scale: an event sorter, for global total order, scalable, resilient, performant.

