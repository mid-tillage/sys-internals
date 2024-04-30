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
- Back-end: Python, Django, Flask
- Databases: MongoDB, PostgreSQL
- Containerization: Docker
- Orchestration: Kubernetes
- Architecture: Microservices and Web Services
- Monitoring: Prometheus and Grafana
- ~~Data Mining: Apache Spark~~

## Repositories:

- https://github.com/mid-tillage/store-customer-web
- ~~https://github.com/mid-tillage/store_customer_py_ws~~
- https://github.com/mid-tillage/store_admin_web
- https://github.com/mid-tillage/store_admin_py_ws
- ~~https://github.com/mid-tillage/store_engine_orchestrator_queue_py_ms~~ (producer)
- ~~https://github.com/mid-tillage/store_sales_worker_py_ms~~ (consumer)
- ~~https://github.com/mid-tillage/store_engine_orchestrator_py_ms~~
- ~~https://github.com/mid-tillage/store_engine_dto_validator_py_ms~~
- ~~https://github.com/mid-tillage/store_engine_dto_to_model_py_ms~~
- ~~https://github.com/mid-tillage/store_engine_persistence_py_ms~~
- ~~https://github.com/mid-tillage/store_engine_eshop_product_loader_py_ms~~
