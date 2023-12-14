# Store Example

## General description
Draft to manage sales on a store.

Type: Proof of Concept

Key Components:
- Consumer Section: web page, web service and non-relational database for efficient data storage.
- Administration Section: web page, web service, and relational database integrated to a transaction processor using a microservices architecture (operations orchestrator for streamlined operations).

## Solution diagram
![store-example drawio](https://github.com/sys-internals/sys-internals/assets/142703856/8c33e6f2-18e2-4241-bc99-e92c1ce0cc2a)


## Technologies Utilized
- Front-end: Angular
- Back-end: Node.js, TypeScript, NestJS
- Databases: MongoDB, PostgreSQL
- Containerization: Docker
- Orchestration: Kubernetes
- Architecture: Microservices and Web Services
- Monitoring: Prometheus and Grafana
- ~~Data Mining: Apache Spark~~

## Repositories:

- https://github.com/sys-internals/store-customer-web
- https://github.com/sys-internals/store-customer-ws
- https://github.com/sys-internals/store-admin-web
- https://github.com/sys-internals/store-admin-ws
- https://github.com/sys-internals/store-engine-orchestrator-ms
- https://github.com/sys-internals/store-engine-dto-validator-ms
- https://github.com/sys-internals/store-engine-dto-to-model-ms
- https://github.com/sys-internals/store-engine-persistence-ms
- https://github.com/sys-internals/store-engine-eshop-product-loader-ms
