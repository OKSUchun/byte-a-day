# Data dimension

#### What is dimension?

* dimensions are attributes of an entity.&#x20;
* some of dimensoins may IDENTIFY an entity (e.g. Defect's ID)
* otheres are just attributes



Dimensions are in 2 types

* Slowly-changing : attributes that changes over time (e.g. favorite foods)
* Fixed : Phone's manufacturer (e.g. Phone's manufacturer)



Knowing your data consumer

* Data Analyst/Data Scientists
  * Easy to query
* Other Data Engineer&#x20;
  * When other data engineers use your data for their data pipeline
* ML models
  * Identifier, and flat
  * similar to data scientist consumers
* Customers
  * Very easy, no data modelling or excel



OLTP vs master vs OLAP

* OLTP(online transaction processing)
  * low latency, low volume
  * 3rd data normalization, minimize data duplication
* OLAP(online analytical processing)&#x20;
  * Optimized for large volume, GROUP BY queries, minimize JOINS
  * Good chunk of data&#x20;
* Master Data&#x20;
  * Optimizes for completeness of entity definitions
  * middle of OLAP & OLTP



OLTP and OLAP is a continuum

Step1: Production Database Snapshots (from OLTP)

Step2: Master Data

* dedup
* Master Data join over 40 tables to integrate data from OLTP.

Step3: OLAP Cubes

* GROUP BY, (by country, by devices)
* slice and dice for analytics&#x20;

Step4: Metrics



Cumulative Table Design

* Holding on to history
* 2 data frames (yesterday and today)&#x20;
* FULL OUTER JOIN two data frames
* COALESCE values to keep everything around???

Usage of cumulative table design

* growth analytics at Facebook
* State transition tracking (if users are active yesterday, but not today, lots of transitions over time)

Diagram of cumulative table design

* Today's data will be an input to tomorrow's yesterday table.
* Easy "transition" analysis
* Drawbacks
  * Can only be backfilled sequentially
    * **Relies on yesterday's data, cannot backfill parallely.**&#x20;
  * PII data can be hard since deleted/ inactive users get carried forward&#x20;
* My insight
  * Initially thought that Bug tracking solution table could be designed as cumulative table
  * But bug tracking solution is not about continuously monitoring everlasting entity.
  * Bug will be resolved or deleted, within certain timeframe.&#x20;
  * No need to cumulatively input the data.



<figure><img src="../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

The compactness vs usability tradeoff

* The most usable tables usually
  * No complex data types
  * Easily manipulated with WHERE and GROUP By
* The most compact tables(not human readable)
  * compressed to be as small&#x20;
  * since network I/O is the most expensive
  * e.g. Consumers highly technical
* middle-ground tables
  * JSON, ARRAY data types
  * e.g. Upstream staging/ master data



Struct vs Array vs Map

* Struct
  * never heard about this
  * Table in table
  * keys are rigidly defined, compression is good
* Map
  * keys are loosely defined
  * Values all have to be the same type
* Array
  * ordinal
  * list of values that all have to be the same type



Temporal Cardinality Explosion of Dimensions

Temporal : information that is associated with time&#x20;

* will organize later when I understand this concept.







