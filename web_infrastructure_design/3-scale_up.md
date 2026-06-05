# 1. Why each additional element was added ?

## Additional server

*The new server allows us to separate services instead of hosting everything together.*

**Benefits**:
- Better performance.
- Easier maintenance.
- Independent scaling.
- Reduced resource contention.

**Instead of**:
```
            Server
              |--- Nginx
              |--- App Server
              |--- MySQL
```

*We now have dedicated servers for specific roles.*

## Second Load Balancer (HAProxy Cluster)

### Why add it ?

*In Task 2, the loas balancer was a SPOF.*

```
            User
              |
            HAProxy
              |
            Servers
```

**If HAProxy fails**:
- Website unavailable

*Adding a second load balancer improves availability.*

```
            HAProxy #1
                 |
            HAProxy #2
```

*If one load balancer fails, the other continues serving traffic.*

### Active-Passive cluster

**A common implementation**:
```
HAProxy #1 -> Active
HAProxy #2 -> Standby
```

**If HAProxy #1 fails**:
- HAProxy #2 becomes Active

*This removes the load balancer as a single point of failure.*

*(An Active-Active setup could also be used depending on the design.)*

# 2. Why split components ?

## Dedicated web servers

**Responsibilities**:
- Serve static files.
- Handle HTTPS requests.
- Reverse proxy requests.

**Benefits**:
- Optimized specifically for web traffic.
- Easier scaling of frontend traffic.

## Dedicated application servers

**Responsibilities**:
- Execute application code.
- Process business logic.
- Communicate with databases.

**Benefits**:
- Application scaling independent of web servers.
- Better resource allocation.

## Dedicated database servers

**Reponsibilities**:
- Store Data.
- Manage queries.
- Handle replication.

**Benefits**:
- Database resources are not shared with web or application services.
- Improved performance.
- Easier backup and replication management.

# 3. Advantages over task 2

## Better high availability

**Before**:
- 1 Load Balancer

**After**:
- 2 Load Balancers

*Failure of one load balancer no longer takes down the site.*

## Better scalability

**Web layer**:
- Add more Nginx servers

**Application layer**:
- Add more application servers

**Database layer**:
- Add replicas

*Each layer can scale independently.*

## Better resource utilization

*Before*:
```
                  Server CPU
                      |--- Web
                      |--- App
                      |--- Database
          Competition between services.
```

*After*:
```
            Web CPU -> Web only
            App CPU -> App only
            DB CPU -> Database only
```

*Resources are used more efficiently.*

## Easier maintenance

**Examples**:
- Restart application servers without touching databases.
- Upgrade Nginx independently.
- Perform database maintenance without impacting web servers.
