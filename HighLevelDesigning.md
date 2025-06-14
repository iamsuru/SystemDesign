# System Design Concepts: High Level Designing

---

## Reliability

**Reliability** refers to a system’s ability to consistently perform its intended functions correctly, even in the face of adverse conditions. A reliable system ensures:

- The application performs the function that the user expected.
- It can tolerate the user making mistakes or using the software in unexpected ways.
- Its performance is good enough for the required use case, under the expected load and data volume.
- The system prevents any unauthorized access and abuse.

If all those things together mean "working correctly," then we can understand **reliability** as:

> _"Continuing to work correctly, even when things go wrong."_

This includes scenarios like hardware failures, network issues, or sudden spikes in usage.

---

## Faults and Fault-Tolerant Systems

**Faults** are the things that can go wrong.  
Systems that anticipate and handle faults are called **fault-tolerant** or **resilient**.

### Types of Faults:

#### Hardware Faults

- Hard disks crash
- RAM becomes faulty
- Power outages
- Unplugged or broken network cables

#### Software Errors

- Software bugs or resource leaks
- Faulty dependencies or cascading failures
- Hidden issues triggered by rare conditions
- Root cause: false assumptions in code

**Solutions**:

- Careful design and assumptions
- Testing (unit, integration, corner cases)
- Isolation, monitoring, and auto-recovery mechanisms

#### Human Errors

- Most outages are due to misconfigurations by humans
- Interfaces should guide correct actions and prevent mistakes
- Use non-production sandbox environments for safe testing
- Reliable systems depend on good design, testing, and safeguards

---

## Scalability

**Scalability** is a system’s ability to handle increased load over time.

- A system that works now may fail with more users or data
- It’s not binary — instead, ask:
  - How will the system grow?
  - How will we add resources?
- Planning for scaling helps maintain reliability as usage increases

---

## Types of Scaling

## Vertical Scaling (Scaling Up)

Vertical scaling means increasing the capacity of a single machine by upgrading its hardware—adding more CPU, RAM, or storage.

### Examples

- Upgrading a server from 4 cores to 16 cores
- Increasing memory from 8GB to 64GB
- Moving from a small EC2 instance to a larger one

### Advantages

- Simpler to implement
- No changes required in the application code
- Good for legacy monolithic systems

### Disadvantages

- Limited by hardware capacity
- Single point of failure
- Often requires downtime for upgrades
- High cost for high-end machines

### When to Use

- Small to medium-scale systems
- Quick performance improvements
- When redesigning for horizontal scale is not feasible

---

## Horizontal Scaling (Scaling Out)

Horizontal scaling means increasing system capacity by adding more machines (nodes) and distributing the load across them.

### Examples

- Adding more app servers behind a load balancer
- Scaling databases with read replicas
- Expanding distributed systems like Kafka, Redis, or MongoDB

### Advantages

- Higher fault tolerance
- Better scalability with increasing traffic or data
- No single point of failure
- More cost-effective at scale

### Disadvantages

- More complex system design and management
- Requires load balancing and stateless design
- Handling consistency and coordination is harder

### When to Use

- High-scale, cloud-native, or microservices-based systems
- Systems needing high availability and distributed architecture
- Applications expecting variable or rapidly increasing load

---

## Comparison Table

| Feature           | Vertical Scaling              | Horizontal Scaling  |
| ----------------- | ----------------------------- | ------------------- |
| Approach          | Upgrade a single machine      | Add more machines   |
| Complexity        | Low                           | High                |
| Fault Tolerance   | Low (single point of failure) | High (redundancy)   |
| Cost at Scale     | High                          | More cost-effective |
| Scalability Limit | Hardware bound                | Nearly unlimited    |
| Common Use Cases  | Monolithic systems            | Distributed systems |

## Latency and Response Time

Latency and response time are often used interchangeably, but they are not the same.

- **Response time** is what the client observes. It includes:

  - The actual time taken to process the request (called **service time**)
  - Network delays
  - Queueing delays

- **Latency** refers specifically to the duration a request waits before being processed
  — the time it remains latent, awaiting service.

In summary:

- **Latency** = Time spent waiting to be processed
- **Response time** = Latency + Service time + Network/queueing delays

## Maintainability

Most of the cost of software is not in its initial development, but in its ongoing maintenance. This includes:

- Fixing bugs
- Keeping systems operational
- Investigating and resolving failures
- Adapting to new platforms or technologies
- Modifying the system for new use cases
- Reducing technical debt and improving code quality
- Adding and integrating new features

Maintaining legacy systems can be difficult, especially when they are poorly documented, overly complex, or built with outdated technologies. These challenges often arise when systems are not designed with maintainability in mind.

To build systems that are easier to maintain and evolve, we should focus on the following three principles:

---

### Operability

**Goal:** Make it easy for operations teams to monitor, manage, and troubleshoot the system in production.

**Practices:**

- Provide clear, actionable logs and metrics
- Implement robust alerting and health checks
- Enable automated deployment, rollback, and scaling
- Use infrastructure-as-code to standardize environments
- Support observability through distributed tracing and dashboards

**Benefits:**

- Faster issue resolution
- Lower downtime
- Easier root-cause analysis
- Reduced on-call burden

---

### Simplicity

**Goal:** Minimize unnecessary complexity so that developers can easily understand and reason about the system.

**Practices:**

- Keep designs modular and loosely coupled
- Use clear naming conventions and consistent patterns
- Prefer readability over cleverness
- Remove unused code and obsolete logic
- Keep documentation up to date

**Benefits:**

- Faster onboarding of new team members
- Fewer bugs due to reduced cognitive load
- Easier debugging and refactoring
- Lower risk when making changes

---

### Evolvability

**Goal:** Make it easy to change and extend the system over time, even as requirements evolve.

**Practices:**

- Apply SOLID principles and good object-oriented design
- Use interfaces and abstractions to isolate components
- Write automated tests to support safe refactoring
- Design APIs and schemas with forward- and backward-compatibility in mind
- Avoid hardcoding business rules or assumptions

**Benefits:**

- Reduced cost of future development
- Greater agility to support new features or markets
- Lower risk of regressions during changes
- Systems stay relevant and useful for longer

---

By designing systems with **operability**, **simplicity**, and **evolvability** in mind, we not only reduce maintenance costs but also improve developer productivity and system reliability over time.

# CAP Theorem

The **CAP Theorem**, introduced by Eric Brewer, describes the fundamental trade-offs in **distributed systems**. It states that a distributed system can only guarantee **two out of the following three** properties at the same time:

## 1. Consistency (C)

Every read receives the **most recent and correct data**, regardless of which node responds.

**Example:**  
In a banking system, after transferring ₹500 from Account A to B, every user (from any server) should immediately see the updated balances.

## 2. Availability (A)

Every request receives a **valid response**, even if some of the data might be stale or outdated. The system stays "available" no matter what.

**Example:**  
In an online shopping app, even if some servers are down, users should still be able to browse products (though prices or stock may not be the latest).

## 3. Partition Tolerance (P)

The system continues to function **even when there is a network partition**, meaning some servers can't communicate with others.

**Example:**  
If two servers in different locations lose connectivity, the system still responds to user requests — possibly using local data — instead of completely failing.

---

## Why You Can Only Choose Two

In real-world systems, **network partitions are inevitable** (so P is a must). That means you must choose between:

### CP (Consistency + Partition Tolerance)

- Guarantees **up-to-date and correct data**, even when network issues occur.
- Might become **unavailable** temporarily to maintain data integrity.

**Used by:**

- HBase
- MongoDB (with strong consistency settings)
- Redis (when configured with master/slave and no auto-failover)

**Use Case:**  
Banking, Payment Processing — data accuracy is more important than uptime.

---

### AP (Availability + Partition Tolerance)

- System is **always available**, even during network failures.
- May serve **stale data** due to lack of consistency.

**Used by:**

- Cassandra
- DynamoDB
- Couchbase

**Use Case:**  
Social Media Feeds, Analytics Dashboards — freshness of data is less important than high availability.

---

### CA (Consistency + Availability) – Theoretical

- Only possible when there is **no network partition**.
- Not achievable in real-world distributed systems where partitions are inevitable.

---

## Summary Table

| Property                    | Description                                    | Real-World Tools        |
| --------------------------- | ---------------------------------------------- | ----------------------- |
| **Consistency (C)**         | All nodes see the same, most recent data       | MongoDB (strong), HBase |
| **Availability (A)**        | System always responds, even if not up to date | DynamoDB, Cassandra     |
| **Partition Tolerance (P)** | System works despite network failures          | All distributed systems |

---

## Choosing the Right Pair

Depending on your system’s requirements:

| Goal                       | Choose This Pair | Why?                                   |
| -------------------------- | ---------------- | -------------------------------------- |
| Financial transactions     | **CP**           | Data correctness is critical           |
| Social media/news feed     | **AP**           | Always available, eventual consistency |
| Internal tools, admin apps | **CP** or **CA** | Often used in safe, low-failure zones  |
| E-commerce site            | **AP**           | Better to show stale data than go down |

---

## Conclusion

You can’t have **Consistency**, **Availability**, and **Partition Tolerance** all at the same time in a distributed system. You must **choose two**, depending on your business needs. Understanding this trade-off helps in designing scalable, fault-tolerant systems.

# Consistency

When more than one clients requests the system, for all such requests it will be called **Consistent**, when each client gets the same data. The data should always be consistent, regardless of who is accessing it.

When more than one client request the system, for all such requests, when different client get different response due to some recent update that has not been committed to all system yet, this reading operation will be called **Dirty Read**.

## Factors to improve consistency

- imporving network speed (bandwidth).
- Stop the reads until all nodes are updated.
- Replication based on distance aware strategies.

### Types

- **Strong** -: When the system does not allow read operations until all the nodes with replicated data are updated.
- **Eventual** -: User reads requests are not halted till all the replicas are update rather the update process is eventual. Some users might recieve old data but eventually all the data is updated to the latest data.
- **Weak** -: Not necessary that all nodes gets sync. Depends on the business logic.

# Load Balancer

A load balancer is a service that distributed incoming traffic accross multiple backend servers to ensure -:

- No single server is overwhelmed.
- High availability.
- Better performance & fault tolerance.

## Types

### Layer 4 (**Transport Layer**)

- Works at TCP/UDP level.
- Rates based on IP address + port.
- Faster but limited intelligence [AWS NLB]

### Layer 7 (**Application Layer**)

- Understands http/https
- Routes based on urls, headers, cookies.
- AWS application Load Balancer, NGINX, HAProxy.

## Features

- Only routes to healthy server.
- Route some user to same backend (if needed)
- Redirects traffic if servers fails.

# Rate Limiter

A rate limiter is used to control the rate of traffic sent by a client or service.

## In HTTP Context

It limits the number of requests a client is allowed to send over a specified time period.

```js
if (count of requests > threshold) {
  "Access blocked"
}
```

### Examples

- No more than 2 POST requests per second
- Maximum 10 account creations per day from a single IP

## Benefits

- Prevents abuse and denial-of-service attacks
- Filters out accidental or automated excess requests
- Reduces infrastructure cost by limiting unnecessary load
- Prevents server overloading
- Enforces fair usage across clients

---

## Rate Limiting Algorithms

### Fixed Window Counter

- Divides time into fixed-size windows (e.g., 1 minute)
- Count requests per client per window
- If count exceeds the limit, block further requests

#### Problem

Can be bypassed at the boundary of two windows. A client can send double the limit if requests are made at the end of one window and the start of the next.

```js
const rateLimitMap = new Map();
const WINDOW_SIZE_IN_MS = 10000; // 10 seconds
const MAX_REQUESTS = 5;

function fixedWindowRateLimiter(req, res, next) {
  const key = req.ip;
  const now = Date.now();

  if (!rateLimitMap.has(key)) {
    rateLimitMap.set(key, { count: 1, startTime: now });
    return next();
  }

  const { count, startTime } = rateLimitMap.get(key);
  if (now - startTime < WINDOW_SIZE_IN_MS) {
    if (count < MAX_REQUESTS) {
      rateLimitMap.set(key, { count: count + 1, startTime });
      return next();
    } else {
      return res.status(429).send("Rate limit exceeded");
    }
  } else {
    rateLimitMap.set(key, { count: 1, startTime: now });
    return next();
  }
}
```

---

### Sliding Window Log

- Stores the timestamp of every request
- On each request:
  - Remove old timestamps beyond the window
  - Count remaining timestamps
  - Block if count exceeds the limit

#### Problem

Consumes more memory since each request is tracked individually.

```js
const requestLogs = new Map();
const WINDOW_SIZE_IN_MS = 10000;
const MAX_REQUESTS = 5;

function slidingWindowLogRateLimiter(req, res, next) {
  const key = req.ip;
  const now = Date.now();

  if (!requestLogs.has(key)) {
    requestLogs.set(key, []);
  }

  const timestamps = requestLogs
    .get(key)
    .filter((ts) => now - ts < WINDOW_SIZE_IN_MS);

  if (timestamps.length < MAX_REQUESTS) {
    timestamps.push(now);
    requestLogs.set(key, timestamps);
    return next();
  } else {
    return res.status(429).send("Rate limit exceeded");
  }
}
```

---

### Sliding Window Counter

- Time window is divided into smaller sub-windows (e.g., 60 seconds into 6 sub-windows of 10 seconds)
- Stores only the request count per sub-window
- On each request:
  - Remove expired sub-windows
  - Sum all valid counts
  - Allow or block based on the sum

#### How It Works:

- Define a fixed time window, say 60 seconds (1 minute).
- Split it into N sub-windows, e.g., 6 sub-windows of 10 seconds each.
- For every request:
  Update the count in the current sub-window.
  Remove expired sub-windows (older than 1 minute).
  Sum all counts from valid sub-windows.
  If total > limit → block the request.

- Result: Smoother, more fair rate limiting without boundary problems.

#### Example

```js
  Window = 60 seconds
  Sub-window = 10 seconds
  Max Requests = 100

  Let’s say the current time is 12:00:35.
  Your stored sub-windows:
  [
  { timestamp: 12:00:05, count: 10 },
  { timestamp: 12:00:15, count: 30 },
  { timestamp: 12:00:25, count: 25 },
  { timestamp: 12:00:35, count: 20 }
  ]
  Total requests in last 60 seconds: 10 + 30 + 25 + 20 = 85

  If a new request comes in:

  Add it to the current sub-window (12:00:35)

  New total = 86 -> Allowed

  If total > 100 -> Block
```

#### Problem

Less precise than full log but much more memory efficient.

```js
const subWindows = new Map();
const WINDOW_SIZE = 60000; // 1 minute
const SUB_WINDOW_SIZE = 10000; // 10 seconds
const MAX_REQUESTS = 10;

function slidingWindowCounterRateLimiter(req, res, next) {
  const key = req.ip;
  const now = Date.now();

  if (!subWindows.has(key)) {
    subWindows.set(key, []);
  }

  let buckets = subWindows
    .get(key)
    .filter((b) => now - b.timestamp < WINDOW_SIZE);
  const lastBucket = buckets[buckets.length - 1];

  if (lastBucket && now - lastBucket.timestamp < SUB_WINDOW_SIZE) {
    lastBucket.count += 1;
  } else {
    buckets.push({ timestamp: now, count: 1 });
  }

  const total = buckets.reduce((sum, b) => sum + b.count, 0);
  subWindows.set(key, buckets);

  if (total > MAX_REQUESTS) {
    return res.status(429).send("Rate limit exceeded");
  }

  return next();
}
```

#### Real-World Use:

Often used in APIs, backend services, or custom Redis-based rate limiting systems where you want more fair usage across time without storing every single request.

---

### Token Bucket

- A token bucket holds tokens and refills at a fixed rate
- Each request consumes one token
- If tokens are available, the request is allowed
- If not, the request is blocked

#### Advantage

Allows short bursts of traffic but enforces rate over time meaning that server did not recieved any requests for 1 mins so a lot of token was available in the bucket so after the 5th minute server started recieving reqests so as multiple requests will hits the server and it has to process all but if less taken is available then that's fine.

```js
const tokenBuckets = new Map();
const MAX_TOKENS = 10;
const REFILL_RATE = 1; // 1 token every 100ms
const REFILL_INTERVAL = 100;

function tokenBucketRateLimiter(req, res, next) {
  const key = req.ip;
  const now = Date.now();

  if (!tokenBuckets.has(key)) {
    tokenBuckets.set(key, { tokens: MAX_TOKENS, lastRefill: now });
  }

  const bucket = tokenBuckets.get(key);
  const elapsed = now - bucket.lastRefill;
  const refill = Math.floor(elapsed / REFILL_INTERVAL) * REFILL_RATE;

  bucket.tokens = Math.min(MAX_TOKENS, bucket.tokens + refill);
  bucket.lastRefill = now;

  if (bucket.tokens > 0) {
    bucket.tokens -= 1;
    return next();
  } else {
    return res.status(429).send("Rate limit exceeded");
  }
}
```

---

### Leaky Bucket

- Requests enter a queue (bucket)
- The queue is processed at a fixed rate (leaks)
- If the bucket is full, new requests are dropped

#### Advantage

Ensures a consistent rate of processing regardless of request burst

```js
const leakyBuckets = new Map();
const MAX_QUEUE_SIZE = 10;
const LEAK_INTERVAL = 100; // one request per 100ms

function initLeakyBucketCleaner(key) {
  const queue = leakyBuckets.get(key);
  if (!queue._cleanerStarted) {
    queue._cleanerStarted = true;
    setInterval(() => {
      if (queue.length > 0) queue.shift();
    }, LEAK_INTERVAL);
  }
}

function leakyBucketRateLimiter(req, res, next) {
  const key = req.ip;
  if (!leakyBuckets.has(key)) {
    const queue = [];
    queue._cleanerStarted = false;
    leakyBuckets.set(key, queue);
    initLeakyBucketCleaner(key);
  }

  const queue = leakyBuckets.get(key);

  if (queue.length >= MAX_QUEUE_SIZE) {
    return res.status(429).send("Rate limit exceeded");
  }

  queue.push(Date.now());
  return next();
}
```

---

## Algorithm Comparison

| Algorithm              | Burst Support | Accuracy | Memory Use | Use Case                   |
| ---------------------- | ------------- | -------- | ---------- | -------------------------- |
| Fixed Window Counter   | No            | Low      | Low        | Simple APIs                |
| Sliding Window Log     | Yes           | High     | High       | Accurate request tracking  |
| Sliding Window Counter | Yes           | Medium   | Medium     | Balanced performance       |
| Token Bucket           | Yes           | Medium   | Low        | APIs with burst allowance  |
| Leaky Bucket           | No            | High     | Low        | Consistent processing rate |

---

## Notes

- In distributed systems, use Redis or other shared storage for rate limit state
- Consider using libraries like:
  - express-rate-limit
  - rate-limiter-flexible
- External reverse proxies (like NGINX, Cloudflare, AWS API Gateway) also support built-in rate limiting

# Database Sharding

Database sharding is a database partitioning technique used to split large databases into smaller, faster, and more manageable parts called **shards**.
Each shard is an independent database that contains a subset of the data.

---

## Why Use Sharding?

- To handle large volumes of data.
- To improve write and read performance.
- To reduce latency and load on a single database server.
- To enable horizontal scaling by adding more machines instead of upgrading a single server.

---

## How Sharding Works

1. Choose a **sharding key** (for example, `user_id`).
2. Based on this key, route the data to a specific shard.
3. Each shard stores only a portion of the full dataset.

---

## Example

Consider a `users` table with millions of rows. You decide to shard it based on `user_id`.

| user_id     | Shard   |
| ----------- | ------- |
| 1 - 1000    | Shard A |
| 1001 - 2000 | Shard B |
| 2001+       | Shard C |

Each shard contains only a specific range (or hash) of user data and operates independently.

---

## Benefits

- **Scalability**: Add more shards as the data grows.
- **Performance**: Each shard holds less data, so queries run faster.
- **Isolation**: Failures in one shard do not affect the others.

---

## Challenges

- Queries spanning multiple shards become more complex.
- Cross-shard joins are difficult or inefficient.
- Rebalancing shards is challenging when data grows unevenly.

## Sharding Strategies

### Hash-Based Sharding

- A hash function is applied to the sharding key (e.g., user_id).
- The result of the hash decides which shard the data goes to.

**Example**
Suppose you have 3 shards:

```js
shard_number = hash(user_id) % 3;
```

| user_id | hash(user_id) | shard_number | Shard   |
| ------- | ------------- | ------------ | ------- |
| 1001    | 3487234       | 1            | Shard 1 |
| 1002    | 9872398       | 2            | Shard 2 |
| 1003    | 1122334       | 0            | Shard 0 |

So the data is uniformly distributed across all shards based on the hash result.

#### Pros

- Uniform data distribution across shards.
- Prevents hotspots, since distribution is randomized.

#### Cons

- Range queries (e.g., `user_id BETWEEN 1000 AND 2000`) are inefficient.
- Resharding (adding/removing shards) is complex because the hash function's output changes.

---

### Range-Based Sharding

#### How It Works

- The sharding key is divided into predefined ranges.
- Each shard is assigned a specific range of values.

#### Example

| Shard   | user_id Range   |
| ------- | --------------- |
| Shard A | 1 - 100000      |
| Shard B | 100001 - 200000 |
| Shard C | 200001 - 300000 |

If `user_id = 150000`, the data goes to **Shard B**.

#### Pros

- Simple and efficient for range queries.
- Easy to implement and understand.

#### Cons

- Can lead to uneven data distribution (hotspots).
- Requires manual rebalancing if one shard grows faster than others.

---

### Comparison Summary

| Strategy             | Data Distribution    | Range Queries | Uniform Load   | Resharding Complexity  |
| -------------------- | -------------------- | ------------- | -------------- | ---------------------- |
| Hash-Based Sharding  | Based on hash(key)   | No            | Yes            | High                   |
| Range-Based Sharding | Based on value range | Yes           | Not guaranteed | Medium (manual effort) |

---

## Database Replication

Replication is the process of applying data from one database server(**master**) to one or more servers(**replica**)

# Database Replication

## What is Database Replication?

Database replication is the process of copying data from one database server (source) to one or more other servers (replicas).

The goal is to ensure that the same data exists across multiple database instances, improving read performance, redundancy, and availability.

---

## Why Use Replication?

- **High Availability**: Ensures the system remains operational even if one database server fails.
- **Read Scalability**: Read queries can be distributed across replicas to balance the load.
- **Disaster Recovery**: Replicas can act as backup copies.
- **Geo-Distribution**: Replicas can be deployed in different regions to reduce latency for global users.

---

## Types of Replication

### 1. Master-Slave Replication (Primary-Secondary)

#### How It Works

- The **master** (also called **primary**) handles all **write** operations.
- The **slaves** (also called **secondaries**) replicate data from the master and are used for **read-only** operations.

#### Pros

- Writes and reads are separated.
- Read performance improves due to distribution across slaves.

#### Cons

- Only one write node (the master) becomes a bottleneck.
- Replication lag may lead to inconsistent reads.
- Requires manual or automated failover if the master fails.

---

### 2. Read Replicas

#### How It Works

- A type of master-slave setup.
- Read replicas replicate data from the primary and are used solely for read queries.
- Popular in cloud-based databases like AWS RDS.

#### Example Use Case

- Master handles inserts and updates.
- Read replicas serve analytics dashboards or high-throughput API reads.

#### Pros

- Easy to scale reads horizontally.
- Simple to set up in managed cloud services.

#### Cons

- Writes still go through the primary.
- Replication lag can affect real-time consistency.

---

### 3. Multi-Master Replication

#### How It Works

- Multiple database nodes can accept both **read and write** operations.
- Each node replicates changes to the others.

#### Pros

- Higher availability and fault tolerance.
- No single point of failure for writes.

#### Cons

- Requires conflict detection and resolution.
- Harder to maintain strong consistency.

---

## Comparison Table

| Replication Type | Write Support | Read Scalability | Conflict Handling | Common Use Case              |
| ---------------- | ------------- | ---------------- | ----------------- | ---------------------------- |
| Master-Slave     | Only Master   | Yes              | Not Required      | General-purpose applications |
| Read Replicas    | Only Master   | Yes              | Not Required      | Read-heavy workloads         |
| Multi-Master     | All Nodes     | Yes              | Required          | Distributed systems          |

---

## Cache

Caching is a process of storing frequently accessed data in a fast-access layer (_like memory_), so that future requests for that data can be server faster.

- It is temporary storage.
- It stores the frequently accessed data and results of expenses responses.

## Redis Basics

### What is Redis?

**Redis** (REmote DIctionary Server) is an open-source, in-memory data structure store used as a **cache**, **database**, and **message broker**.

### Key Features

- In-memory key-value store
- Extremely fast read and write operations
- Rich data structures: strings, hashes, lists, sets, sorted sets, bitmaps, hyperloglogs
- Supports persistence using RDB snapshots and AOF logs
- Built-in replication and pub/sub capabilities
- Key-level TTL (Time-To-Live) support

### Common Use Cases

- Caching expensive database queries
- Storing web sessions
- Leaderboards and counters
- Rate limiting
- Message queues and pub/sub systems

### Common Redis Commands

```bash
SET key value            # Set a key
GET key                  # Get the value of a key
DEL key                  # Delete a key
EXPIRE key 60            # Set expiration of 60 seconds
TTL key                  # Get remaining TTL of a key
INCR key                 # Increment a counter
LPUSH list value         # Push to a list (left side)
SADD set value           # Add to a set
```

## Caching Strategies: TTL, LRU

### TTL (Time-To-Live)

Sets an expiration time on cache entries.
Useful for data that becomes stale over time (e.g., weather data, session tokens).
Redis allows setting TTL using EXPIRE or directly via SET key value EX seconds.

#### Pros:

Auto-expiry prevents stale data.
Helps manage memory usage.

#### Cons:

Requires good judgment on TTL duration.
Expired cache can cause temporary performance drop due to cache miss.

### LRU (Least Recently Used)

Automatically evicts the least recently used keys when memory is full.
Redis supports this through configuration:

```bash
  maxmemory 100mb
  maxmemory-policy allkeys-lru
```

#### Pros:

Keeps hot/frequent data in cache.
Fully automatic eviction.

#### Cons:

Some rarely used but important data may be evicted.
Not deterministic — eviction depends on access patterns.

### CDN (Content Delivery Network)

A CDN is a geographically distributed network of proxy servers and data centers that deliver static content (like images, JS/CSS, videos) to users based on their location.

#### How It Works

User requests go to the nearest CDN edge node.
If content is cached there, it’s served directly.
If not, the edge node fetches it from the origin server, caches it, and serves it.

#### Benefits

Reduces latency (delivers from edge)
Offloads traffic from origin servers
Improves reliability and fault tolerance
Helps absorb large traffic spikes (e.g., DDoS mitigation)

**Common CDNs**

- Cloudflare, Akamai, Amazon CloudFront, Fastly, Google Cloud CDN

---
