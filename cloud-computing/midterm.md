
# Table of Contents

1.  [Cloud Computing - Midterm review](#org34a7ead)
    1.  [Infrastructure as Code (IaC)](#orgc4f8d71)
        1.  [Usage in Cloud Computing](#orgd0f7ab0)
        2.  [Aspects of Infrastructure Where IaC Could Not Be Employed and Why](#org4b7f2de)
        3.  [Solutions / Workarounds](#org8eed22c)
    2.  [CAP and PACELC Theorem](#orgd8867e4)



<a id="org34a7ead"></a>

# Cloud Computing - Midterm review


<a id="orgc4f8d71"></a>

## Infrastructure as Code (IaC)

The practice of provisioning and managing infrastructure through code rather
than relying on graphical user interfaces or manual processes.


<a id="orgd0f7ab0"></a>

### Usage in Cloud Computing

-   Developed Terraform configurations that defined the desired resources,
    including EC2 instances and S3 buckets, with settings customized to meet
    project specifications, such as instance type and operating system image.

-   Executed Terraform commands to implement the configurations established in my
    files within my AWS account, employing commands such as \`terraform plan\` and
    \`terraform apply\`.
    -   The &ldquo;terraform apply&rdquo; command read the configuration files, validated the
        corresponding resources on AWS, and created or updated those resources as
        required.


<a id="org4b7f2de"></a>

### Aspects of Infrastructure Where IaC Could Not Be Employed and Why

-   Validating the solution: Confirming that the service I created was fully
    functional.
-   Deploying code: Initiating my application on the server.
-   Uploading assets: Transferring images to S3.
-   Seeding the database: Running the SQL script to set up the schema, service
    account, and other related components.

The primary reason for these limitations is that Terraform is intended for
resource instantiation rather than for interacting with or modifying their
internal configurations. Additionally, Terraform&rsquo;s approach to change
identification does not effectively manage non-idempotent actions.


<a id="org8eed22c"></a>

### Solutions / Workarounds

Developing a script that either utilized the AWS CLI to upload images to S3,
utilized Terraform outputs in conjunction with an SSH key to transfer and
initiate the service, or allowed direct access to the database to execute schema
setup statements.


<a id="orgd8867e4"></a>

## CAP and PACELC Theorem

In a distributed system, such as a database across multiple servers, it is only
possible to achieve, at most, two out of three: Consistency, Availability, and
Partition Tolerance.

It is important to note that a Consistency-Availability (CA) system may struggle
to recover from network disconnections, leading to lost updates and
irreconcilable data divergence. The real trade-off lies between
Consistency-Partition Tolerance (CP) and Availability-Partition Tolerance
(AP)—either sacrificing Consistency or Availability during a network partition.
Additionally, even with a stable network, prioritizing Consistency can result in
increased latency.

Choosing between Consistency and Availability/Low Latency:

-   Evaluate how tolerant the business requirements are to data inconsistency. How
    permissible is it for two instances of the same query to produce differing
    results? For example, in a financial transaction, do all parties need to have
    the same information about the transaction&rsquo;s approval or completion, or can
    one party be informed that it&rsquo;s pending while another is told it&rsquo;s completed?
-   Assess the turnaround time requirements for the system—how critical is it to
    minimize request latency?
-   Determine the necessary availability for the system—how acceptable is system
    downtime in the event of a network outage?

