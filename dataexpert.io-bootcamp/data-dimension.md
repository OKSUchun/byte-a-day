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





