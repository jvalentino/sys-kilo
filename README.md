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

Web Domain

- TBD

Document Domain

- TBD

User Domain

- TBD

Reporting Domain

- TBD

Monitoring Domain

- TBD

Logging Domain

- TBD

Eventing Domain

- TBD

# Table of Contents

TBD

# Previous System

TBD

# Current System

TBD

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



