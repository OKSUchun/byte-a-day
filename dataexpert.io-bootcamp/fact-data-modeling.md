# Fact Data Modeling

## What is Fact

Fact&#x20;

* something that happened or occurred&#x20;
* facts are not slowly changing dimensions&#x20;



* Fact data needs context to do the better analysis
  * Fact data can need a lot of context data



## Modelling Fact data&#x20;

* Normalization vs Denormalization&#x20;
  * in small size, normalization don't have any dimensional attributes, only IDs to join to get that information
  * in big scale, denormalized facts bring in dimensional attributes for quicker analysis



## Raw Logs vs Fact data

* Raw logs
  * no quality gurantees
* Fact data
  * trust of data is higher



## How does fact modeling work&#x20;

* who, what, when, where, how
* Who: ID&#x20;
* Where: device\_id
* How:&#x20;
* What: fields are fundamentally part of the natrue of the fact&#x20;
  * notification world: "GENERATED", "SENT", "CLICKED", "DELIVERED"
  * order: "Ordered", "Shipped", "Sent", "Delivered"
* When:&#x20;
  * mostly an "event\_timestamp or "event\_date"
  * aware that time between different systems are the same



* Fact data need quality gurantees
* Fact data should be smaller than raw logs
* Fact data should parse out hard to understand columns
  * only strings, integers, enum
  * Meaning should not have massive json, and blob



## How does logging fit inot fact data?

* Logging brings in all the critical context for you fact data
* don't log everything
  * Log only what you really need
* Logging should conform to values specified by the online teams





## Potential options when working with high volume fact data

* Sampling&#x20;
  * Doesn't work for all use cases, works best for metric-driven use cases where imprecision isn't an issue
* Bucketing
  * Fact data can be bucketed by one of the important dimensions&#x20;
