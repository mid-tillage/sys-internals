# Store Example

## General description
Draft to manage sales on a store.

Type: Proof of Concept

Key Components:
- Consumer Section: web page, web service and non-relational database for efficient data storage.
- Administration Section: web page, web service, and relational database integrated to a transaction processor using a microservices architecture (operations orchestrator for streamlined operations).

## Solution diagram
![store-example drawio](https://github.com/mid-tillage/sys-internals/assets/142703856/8c33e6f2-18e2-4241-bc99-e92c1ce0cc2a)
 
## Technologies Utilized
- Front-end: Angular
- Back-end: Go, gRPC (pending)
- Databases: MongoDB, PostgreSQL
- Containerization: Docker
- Orchestration: Kubernetes
- Architecture: Microservices and Web Services
- Monitoring: Prometheus and Grafana
- ~~Data Mining: Apache Spark~~

## Repositories:
- https://github.com/mid-tillage/store-customer-web
- ~~https://github.com/mid-tillage/store-customer-go-ws~~
- https://github.com/mid-tillage/store-admin-web
- https://github.com/mid-tillage/store-admin-go-ws
- https://github.com/mid-tillage/store-engine-orchestrator-queue-go-ms (producer)
- https://github.com/mid-tillage/store-sales-worker-go-ms (consumer)
- ~~https://github.com/mid-tillage/store-engine-orchestrator-go-ms~~
- ~~https://github.com/mid-tillage/store-engine-dto-validator-go-ms~~
- ~~https://github.com/mid-tillage/store-engine-dto-to-model-go-ms~~
- ~~https://github.com/mid-tillage/store-engine-persistence-go-ms~~
- ~~https://github.com/mid-tillage/store-engine-eshop-product-loader-go-ms~~


