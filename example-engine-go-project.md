# Engine example
## General description
Type: Proof of Concept

Key Components:
- Server: handles incoming payload through port 8080
- Clients:
  - Persist event received by engine to pg db.
  - Process sale order.
 
## Technologies Utilized
- Front-end: TBD
- Back-end: Go, gRPC (pending)
- Databases: MongoDB, PostgreSQL
- Containerization: Docker
- Orchestration: Kubernetes
- Architecture: Microservices

## Repositories:
- https://github.com/sys-internals/example-engine-go
