
# Table of Contents

1.  [Server Scaling (Databases & All the other goodies)](#orgb055faa)
    1.  [When should you scale your Database?](#orgaf22691)
    2.  [Database Scaling](#org3221357)

<a id="orgb055faa"></a>

# Server Scaling (Databases & All the other goodies)

Servers typically scale in two ways:

-   Vertical: Adding more resources to the server; enhancing it with more RAM or
    processing power.

-   Horizontal: Adding a copy of the server; a whole new instance to help handle
    increased traffic.

One downside of vertical scaling is that it&rsquo;s a single point of failure, so you
have to consider some aspects before selecting this option:

-   What is your uptime requirement?
    -   Depending on your SLAs, having a single database can cause you to miss those
        SLAs; i.e., 99% uptime means approximately 7.3 hours in a month.

-   How will you mitigate the single point of failure aspect?
    -   Backups?
        -   Essentially, these are complete copies of your data or server that can be
            used to fully restore the state of your server.
        -   Typically, this approach does not scale as the data size increases and
            takes more time to copy the data.
    
    -   Replicas?
        -   Essentially a copy of your server that runs concurrently and synchronizes
            with your main service, acting as a backup for when one fails.
        -   How will you perform the synchronization?
            -   Asynchronous?
                -   Attempt to write; it&rsquo;s acceptable if it doesn&rsquo;t confirm
                    synchronization.
                -   In the case of a network partition, if an async synchronization
                    request fails, this can lead to some drift between the replicas. If
                    the synchronization is not retried or completed, there is a chance
                    that the replica will be out of sync, which could cause data loss if
                    the up-to-date server faults.
            -   Synchronous?
                -   A request is not complete until all replicas respond.


<a id="orgaf22691"></a>

## When should you scale your Database?

There are a few concerns that may prompt you to scale your service:

1.  Resilience & Availability:
    -   What are my SLAs? Are you able to meet them with your current
        infrastructure? Are you okay with some data loss?

2.  Throughput:
    -   Reads vs Writes
        -   In the case where you get more reads than writes, perhaps having a
            primary-replica setup with the replica handling a bit of the read
            requests might help your service scale a bit better.
        -   In the case of writes, the situation is more nuanced:
            -   **Partitioning**:
                -   Partitioning is a strategy of breaking down a table into slices, with
                    each partition typically having its own index. It&rsquo;s a strategy one
                    can use if they are sure that there won&rsquo;t be any cross-partition
                    requests, as those are typically costly.
                -   In essence, partitioning utilizes the same database but
                    &ldquo;partitioned&rdquo;/&ldquo;split&rdquo; tables.
            -   **Sharding**:
                -   Separate your model into different tables, often in different
                    servers; in this way, data is only grouped with related data.
                -   In essence, sharding is splitting your schema/models into separate
                    databases.

3.  Transactionality:
    -   If you need to have &ldquo;transactions&rdquo; involving multiple changes to multiple
        tables that fail if even one of the changes fails (ACID). Relational
        databases provide this feature to their users while non-relational
        databases do not; you can modify tables simultaneously, but there is a high
        likelihood that there will be no form of built-in transactions. This can be
        mitigated/solved on the client side by implementing some form of
        transaction, i.e.
        
            try {
              insert 1
            } catch {
              try? {
                delete 1
              } catch {
                ?? // What if the delete fails? There is no guarantee that client-side transactions will work out
              }
            } else {
              try {
                insert 2
              } ...
            }

4.  Referential integrity:
    -   Much like Transactionality this is supported heavily by relational
        databases than it is in non-relational. It&rsquo;s possible to only insert a row
        based on the existence of another in relational databases, mitigating
        another instance removing it or changing it while you work on it.
    
    -   A way to handle this for non-relational databases, is to have some sort of
        lock that is held by a client stating &ldquo;Hey, I&rsquo;m working on this. I&rsquo;m client
        A and I got the lock at <time> and this lock expires at <time> or when I
        release it. So don&rsquo;t change this record.&rdquo; The other way is client-side
        enforcement.

One thing to note is that not all systems are concerned with the above; Some
systems might not have any transactions and might not require ACID compliance.
As such you should focus on two aspects &ldquo;Accuracy&rdquo; & &ldquo;Precision&rdquo;:

-   **Accuracy**: How accurate are you with your suggestions.
-   **Precision**: How does that fit to your particular use case.

It&rsquo;s usually a happy medium between accuracy and precision.


<a id="org3221357"></a>

## Database Scaling

Depending on the type of database (Relational, Key-Value, Document), you may be
advised or encouraged to follow a specific scaling strategy.

Relational databases typically favor vertical scaling, while Key-Value and
Document databases typically favor and are built for horizontal scaling with the
added cost of being harder to build a data model for non-relational databases.

