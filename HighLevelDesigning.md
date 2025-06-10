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
