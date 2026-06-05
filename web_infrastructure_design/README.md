# Web Infrastructure Design

This project focuses on designing and evolving a web infrastructure for the website **www.foobar.com**.

The architecture evolves through four stages, from a simple single-server setup to a salable, secured and highly available distributed system.

---

## 0. Simple Web Stack

A single server hosts all components of the application:
- Nginx web server
- Application server
- MySQL database
- Application code

### Characteristics

- Domain name (foobar.com) points to the server via DNS (A record).
- HTTP is used for communication between users and the server.

### Issues

- Single Point of Failure (SPOF)
- No scalability
- Downtime during maintenance
- Limited resources (CPU, RAM, storage)

---

## 1. Distributed Web Infrastructure

The system is split across multiple servers and introduce a load balancer.

### Components

- HAProxy load balancer
- 2 application servers (Nginxs + application code)
- MySQL Primary-Replica database

### Key improvements

- Load balancing using Round Robin
- Active-Active web servers
- Database replication (Primary handles writes, Replica handles reads)

### Issues

- Load balancer is a SPOF
- Primary database is a SPOF
- No security layer (no firewall, no HTTPS)
- No monitoring system

---

## 2. Secured and Monitored Web Infrastructure

Security and observability are added to the distributed system.

### Additions

- 3 firewalls (one per server)
- HTTPS using SSL certificate
- Monitoring agents on each server

### Improvements

- Encrypted traffic using TLS
- Traffic filtering via firewalls
- Metrics and logs collection through monitoring tools

### Issues

- SSL termination at load balancer (internal traffic not encrypted)
- Single writable MySQL node remains a bottleneck
- Identical server design increases coupling and resource contention

---

## 3. Scale Up

The architecture is improved for scalability and separation of concerns.

### Additions

- Additional server
- HAProxy cluster (2 load balancers)
- Dedicated servers for:
  - Web layer (Nginx)
  - Application layer
  - Database layer

### Improvements

- Load balancer no longer a single point of failure
- Each layer can scale independently
- Better resource isolation and performance
- Easier maintenance and deployment

---

## Final Goal

The evolution of this infrastructure demonstrates:
- How to remove SPOFs
- How to scale web systems
- How to secure information
- How to monitor production systems
- How to separate system components for better architecture design
