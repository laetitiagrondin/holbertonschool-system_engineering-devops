# 1. Explanation

## Why each additional element was added ?

### Firewalls (3)

**One firewall on each server**:
- Load Balancer
- Server 1
- Server 2

**Purpose**:
- Filter incoming and outgoing traffic.
- Allowonly required ports.
- Block unauthorized access.

**Example**:

**Allow**:
- 80 (HTTP)
- 443 (HTTPS)
- 22 (SSH from trusted IPs)

**Block**:
- Everything else

### SSL certificate

Installed on the Load Balancer.

**Purpose**:
- Enable HTTPS.
- Encrypt communications.
- Protect sensitive information.

**Without SSL**:
```
User <---- Plain Text ----> Server
Anyone intercepting traffic can read it.
```

**With SSL**:
```
User <---- Encrypted ----> Server
Data remains confidential.
```

### Monitoring clients

A monitoring agent runs on each server.

**Examples**:
- Sumo Logic Collector
- Datadog Agent
- Prometheus Node Exporter

**Purpose**:
- Collect metrics.
- Collect logs.
- Detect failures.
- Alert administrators.

## What are firewalls for ?

**Firewalls**:
- Control network access.
- Enforce security rules.
- Prevent unauthorized connections.
- Reduce attack surface.

They act as a barrier between trusted and untrusted networks.

## Why serve traffic over HTTPS ?

**HTTPS provides**:
- *Encryption*: Protects data in transit.
- *Authentication*: Confirms users are talking to the correct server.
- *Integrity*: Prevents data from being modified during transmission.

**Examples of protected data**:
- Passwords
- Cookies
- Credit card information
- User sessions

## What is monitoring used for ?

**Monitoring helps administrators**:
- Detect outages.
- Track performance.
- Analyze resource usage.
- Receive alerts.
- Troubleshoot problems.

**Common metrics**:
- CPU usage
- RAM usage
- Disk usage
- Network traffic
- Request rates
- Error rates

## How does the monitoring tool collect data ?

Monitoring agents installed on each server:
```
          Server
            |
        Monitoring Agent
            |
            v
    Monitoring Platform
```

**The agent**:
- Reads system metrics.
- Reads application logs.
- Sends collected data to a monitoring service.

**The monitoring platform**:
- Stores metrics.
- Displays dashboards.
- Triggers alerts.

## How to monitor web server QPS

QPS = Queries Per Second

*For a web server, it often means*:
Requests handled per second

**To monitor QPS**:
- Configure Nginx access logging.
- Have the monitoring agent collect logs.
- Count requests over time.
- Create dashboards and alerts.

**Example**:
```
1000 requests in 10 seconds
QPS = 100 requests/second
```

**This helps identify**:
- Traffic spikes
- Performance bottlenecks
- Capacity requirements

## 2. Issues with this infrastructure

### SSL Termination at the Load Balancer

**What happens**:
```
             User
              |
            HTTPS
              |
          Load Balancer
              |
             HTTP
              |
        Backend Servers
```

*The connection is encrypted only until the load balancer.*

**Between the load balancer and servers**:
- Traffic is unencrypted.
- Sensitive data could be intercepted on the internal network.

**A more secure solution is end-to-end encryption**:
```
User --> HTTPS --> Load Balancer --> HTTPS --> Servers
```

## Only One MySQL Server Accepting Writes

### Current setup:

```
Primary --> Replica
```

### Problems:

**Single Point Of Failure**

*If the Primary fails*:
- No writes are possible.
- Application functionality may break.

**Write Bottleneck**

*All write operations go to one database.*

*As traffic increases*:
- The Primary may become overloaded.

## Every Server Contains All Components

### Each server runs:
- Nginx
- Application Server
- Database

### Problems:

**Resource Contention**

*Services compete for*:
- CPU
- Memory
- Disk I/O

*Example*:
```
            Database spike
                   |
                   v
            Application slows
                   |
                   v
            Website slows
```

**Difficult Scaling**

*Suppose only the database is overloaded.*
*You cannot scale just the database.*

*You must duplicate*:
- Nginx
- App Server
- Database
even if only one component needs more resources.

**Maintenance Complexity**

*Each server must maintain*:
- Web server
- Application servers
- Database
- This increases operational complexity.
