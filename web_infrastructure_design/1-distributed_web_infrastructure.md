# 1. Explanation

## Why each additional element was added ?

### Load Balancer (HAProxy)

Added because:
- Distributes incoming traffic across multiple servers.
- Prevents one server from becoming overloaded.
- Improves availability.
- Allows one server to fail while the other continues serving requests.

### Second Server

Added because:
- Eliminates the application server as a single point of failure.
- Increases capacity.
- Allows traffic sharing between servers.

### Database Replica

Added because:
- Provides data redundancy.
- Reduces database workload.
- Can handle read operations.
- Helps recovery if the Primary database fails.

## Load Balancer Distribution Algorithm

A common configuration is: *Round Robin*.

How it works:
```
Request 1 -> Server 1
Request 2 -> Server 2
Request 3 -> Server 1
Request 4 -> Server 2
```

Traffic is distribued evenly among available servers.

## Active-Active vs Active-Passive

*Active-Active*

Used in this design.

Load Balancer
--/-----\
Server1 Server2
Active Active

Both servers handle requests simultaneously.

Advantages:
- Better performance.
- Better resource utilization.
- Increased capacity.

*Active-Passive*

Load Balancer
--/-----\
Server1 Server2
Active Standby

Only one server receives traffic.
The standby server becomes active if the main server fails.

Advantages:
- Simpler failover.
- Easier synchronization.

Disadvantage:
- One server remains unused most of the time.

## How a Primary replica cluster works

Primary Database
-------|
-------| Replication
-------v
Replica Database

Primary (Master):
- Accepts writes (INSERT, UPDATE, DELETE).
- Contains the authoritative copy of the data.

Replica (Slave):
- Receives updates from the Primary.
- Usually serves read-only queries.
- Cannot normally accept writes.

## Difference between Primary node and replica node for the application

**Primary**

Used for:
INSERT
UPDATE
DELETE

Example:
INSERT INTO users…

**Replica**

Used for:
SELECT

Example:
SELECT * FROM users;

This reduces load on the Primary database.

2. Issues with this infrastructure

## SPOF (Single Point of Failure)

Several still exits:

**Load Balancer**

Only one HAProxy.

If it fails:
- Users -> X -> Website unreachable
- No traffic reaches the servers.

**DNS**

Only one DNS configuration.

If DNS resolution fails:
- Users cannot find the website.

**Primary Database**

If the Primary database fails:
- No writes possible.

The application may become partially unavailable.

## Security Issues

**No Firewall**

Problems:
- All services may be exposed directly to the Internet.
- Increased attack surface.
- Unauthorized access risks.

**No HTTPS**

Problems:
- Traffic is unencrypted.
- Passwords and sensitive data can be intercepted.
- Vulnerable to Man-in-the-Middle attacks.

## No Monitoring

Problems:
- No visibility into server health.
- No alerts when services fail.
- Difficult troubleshooting.
- Performance issues may go unnoticed.
