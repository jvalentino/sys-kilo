# System Kilo: CQRS on k8s

Prerequisites

- 2-Tier: https://github.com/jvalentino/sys-alpha-bravo
- 2-Tier with Load Balancing: https://github.com/jvalentino/sys-charlie
- 2-Tier with Load Balancing and Database Clustering: https://github.com/jvalentino/sys-delta
- 3-Tier: https://github.com/jvalentino/sys-echo-rest
- 3-Tier with Data Warehousing: https://github.com/jvalentino/sys-foxtrot
- N-Tier on Kubernetes: https://github.com/jvalentino/sys-golf
- Microservices on Kubernetes: https://github.com/jvalentino/sys-juliet

This is an example system that it used to demonstrate different architectural approaches as they relate to scalability. Its core functions are the following:

- The system shall allow a user to add documents
- The system shall version documents
- The system shall allow a user to download a document

This specific implementation takes our existing system, and changes the architecture to CQRS based on Domain Driven Design (DDD):

- **Web Domain**: Consists of Frontend, its BFF, and distributed cache
- **Document Domain**: Consists of its OpenAPI REST interface, a Cassandra NoSQL Database, an Aggregator for assembling the NoSQL model based on the event steam, and shared Event Bus
- **User Domain**: Consists of its OpenAPI REST interface, a Cassandra NoSQL Database, an Aggregator for assembling the NoSQL model based on the event steam, and shared Event Bus
- **Reporting Domain**: Consists of Postges Database, and an Aggregator for assembling the RBDMS model based on the event steam
- **Monitoring Domain**: Consists the Prometheus technology stack, which includes AlertManager for managing alerts.
- **Logging Domain**: Consists of Elasticsearch and Kibana for managing centralized logging.
- **Eventing Domain**: Consists of the Kafka and Zookeeper for managing an Event Bus

## System Components

Web Domain - Relating to the Web User Interface

| Name                | Repository                                        | Purpose                                                      |
| ------------------- | ------------------------------------------------- | ------------------------------------------------------------ |
| sys-kilo-web-ui     | https://github.com/jvalentino/sys-kilo-web-ui     | A ReactJS-based frontend for the web portal.                 |
| sys-kilo-web-ui-bff | https://github.com/jvalentino/sys-kilo-web-ui-bff | A Spring Boot based backend for the frontend web portal, that makes use of a Redis distributed cache for session management. |
| sys-kilo-web-cache  | https://github.com/jvalentino/sys-kilo-web-cache  | A Redis cluster used as a distributed cache for session management. |

Document Domain - Relating to Document Management

| Name                    | Repository                                            | Purpose                                                      |
| ----------------------- | ----------------------------------------------------- | ------------------------------------------------------------ |
| sys-kilo-doc-rest       | https://github.com/jvalentino/sys-kilo-doc-rest       | A Spring Boot based application based on OpenAPI that services as the RESTful interface to the domain based on a Kafka Event Bus and Cassandra NoSQL database. |
| sys-kilo-doc-aggregator | https://github.com/jvalentino/sys-kilo-doc-aggregator | A Spring Boot based application that streams Kafka events and turns them into the appropriate domain representation using a Cassandra NoSQL database. |
| sys-kilo-doc-nosql      | https://github.com/jvalentino/sys-kilo-doc-nosql      | A Cassandra database used for representing a materialized view of the current state of the domain. |

User Domain - Relating to User Management

| Name                     | Repository                                             | Purpose                                                      |
| ------------------------ | ------------------------------------------------------ | ------------------------------------------------------------ |
| sys-kilo-user-rest       | https://github.com/jvalentino/sys-kilo-user-rest       | A Spring Boot based application based on OpenAPI that services as the RESTful interface to the domain based on a Kafka Event Bus and Cassandra NoSQL database. |
| sys-kilo-user-aggregator | https://github.com/jvalentino/sys-kilo-user-aggregator | A Spring Boot based application that streams Kafka events and turns them into the appropriate domain representation using a Cassandra NoSQL database. |
| sys-kilo-user-nosql      | https://github.com/jvalentino/sys-kilo-user-nosql      | A Cassandra database used for representing a materialized view of the current state of the domain. |

Reporting Domain

| Name                          | Repository                                                  | Purpose                                                      |
| ----------------------------- | ----------------------------------------------------------- | ------------------------------------------------------------ |
| sys-kilo-reporting-aggregator | https://github.com/jvalentino/sys-kilo-reporting-aggregator | A Spring Boot application that streams events from the Kafka event bus and turns them into a relational representation using a Postgres database for the purpose of ad hoc reporting. |
| sys-kilo-reporting-db         | https://github.com/jvalentino/sys-kilo-reporting-db         | A Postgres database using for maintaining data for reporting purposes |

Monitoring Domain

| Name                | Repository                                        | Purpose                                                      |
| ------------------- | ------------------------------------------------- | ------------------------------------------------------------ |
| sys-kilo-monitoring | https://github.com/jvalentino/sys-kilo-monitoring | A Prometheus-based technology stack used for monitoring, which consists also of AlertManager and Grafana. |

Logging Domain

| Name                | Repository                                        | Purpose                                                      |
| ------------------- | ------------------------------------------------- | ------------------------------------------------------------ |
| sys-kilo-logging    | https://github.com/jvalentino/sys-kilo-logging    | An Elasticsearch implementation used for maintaining centralized logs. |
| sys-kilo-logging-ui | https://github.com/jvalentino/sys-kilo-logging-ui | A Kibana implementation used for visualizing Elasticsearch logs. |

Eventing Domain

| Name              | Repository                                      | Purpose                                          |
| ----------------- | ----------------------------------------------- | ------------------------------------------------ |
| sys-kilo-eventing | https://github.com/jvalentino/sys-kilo-eventing | A Kafka implementation for running an event bus. |

# Table of Contents

TBD

# Previous System

**Microservices and BFFs (Rating: 100k)**

Want to do more? Run more stuff in parallel. Considering that most systems have more than one point of graphical interaction, it is helpful to use the BFF pattern, which has a dedicated backend for each front-end. This both alleviates the load on the solo backend application group, but also allows for specific services for specific clients, as it is highly unlikely that the mobile app receives and sends the same data as the web portal, for example. This also allows each BFF to manage its own session independently, and via a distributed cache insteaf of a database because it is significantly faster. Each of these BFFs is also storing its own data in a NoSQL database, which is faster than RBDMS, with the tradeoff being that you have duplicate data (which isn't really a problem).

The other big change is that the single backend application was broken into multiple applications based on service area. This is highly dependent on what the system does, but allows you to scale at a level of granuliaty that was not possible prior.

[![01](https://github.com/jvalentino/clothes-closet-wiki/raw/main/wiki/step-9.png)](https://github.com/jvalentino/clothes-closet-wiki/blob/main/wiki/step-9.png)

Pros

- Backend and Database independent, allowing us have different optimized servers.
- Multple backends allows us to handle more load from users.
- A database cluster removes the database from being the solo bottlekneck.
- Session is maintained in the database, taking it out of memory.
- Separation between backend and frontend allows for slightly more load.
- Data is continually and selectively pruned from the system to mitigate sizing issues.
- Running the applications architecures on an elastic container platform allows them to scale up and down as needed.
- Using database as a service removed the need to deal with the details yourself.
- Highly granular scaling possible.
- Usage of BFF caching and NoSQL greatly increases scalability.

Cons

- Reliance on RDMS limits upper scalability.

# Current System

**CQRS and Domain Driven Design (Rating: 1m)**

To understand what this means you first need to be familiar with:

1. CAP Theorem
2. Eventual Consitentancy
3. CQRS
4. Domain Driven Design

What the following system does is eliminate the RDBMS as the source of truth, and instead relies on a stream of events by which each domain builds their own representation of that truth based on the individual needs. This critical impact here is that technologies like Kafka are able to handle billions of events per seconds (see LinkedIn) as opposed to traditional messages buses, and well beyond the rate of any NoSQL or RDBMS. The end-result of this though is that the system is eventually consistent, requiring a different approach to reading and writing data, which is where CQRS comes in to play.

- RESTful services get data by directly pulling it using a query out of a NoSQL database.
- If a RESTful services needs to create, update, or delete data it has two options: 1) Call another RESTful service, 2) dispatch the appropriate event to the Event Bus.
- Aggregators are responsible for streaming events from the Event Bus, and using the relevant events to construct the data in their domain specific materlialized views.

The domain driven design aspect can be seen in the organization of the system into domains. Each domain functions like its own system, with its only connection outside of the domain being the Event Bus, and knowledge of other RESTful services.

[![01](https://github.com/jvalentino/clothes-closet-wiki/raw/main/wiki/step-10.png)](https://github.com/jvalentino/clothes-closet-wiki/blob/main/wiki/step-10.png)

Pros

- Backend and Database independent, allowing us have different optimized servers.
- Multple backends allows us to handle more load from users.
- A database cluster removes the database from being the solo bottlekneck.
- Session is maintained in the database, taking it out of memory.
- Separation between backend and frontend allows for slightly more load.
- Data is continually and selectively pruned from the system to mitigate sizing issues.
- Running the applications architecures on an elastic container platform allows them to scale up and down as needed.
- Using database as a service removed the need to deal with the details yourself.
- Highly granular scaling possible.
- Usage of BFF caching and NoSQL greatly increases scalability.
- Capable of billions of events per second with the right approach.

Cons

- Complexity.



# Architecture

## Key Concepts and Technologies

- Cloud Provider
- SaaS
- PaaS
- Image Registry
- Containerization
- Kubernetes
- Helm
- Docker
- Monitoring & Alerting
- Centralized Logging
- Backend for Front-end (BFF)
- Microservices
- CQRS
- CAP Theorem
- Domain Driven Design

## Production

TBD

## Local (Demonstration)

TBD



