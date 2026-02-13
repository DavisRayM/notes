
# Table of Contents

1.  [Deployments](#orgd9323b6)
    1.  [EC2 (Provided OS Images):](#orgc616909)
        1.  [Custom AMI (Custom OS Images)](#orged869b4)
    2.  [Lambda:](#orgd0b0dd6)
    3.  [Docker (Containerization)](#org108b5f7)
        1.  [Container Engine](#org8e4ebbc)
        2.  [Container Orchestrators (Kubernetes, Swarm)](#org53ab5cc)



<a id="orgd9323b6"></a>

# Deployments


<a id="orgc616909"></a>

## EC2 (Provided OS Images):

Mostly for workloads that require some sort of state to be persisted for a long
while or long-running collections of tasks.

Typically, virtual machine services require the below actions to be taken during
deployment:

-   **Steps**:
    -   Install language runtime (Node, JVM) & package manager
    -   Install dependencies (libraries: OpenSSL)
    -   Create a service definition:
        -   Something like \`systemd\` to control the service in all its lifecycles
            (start, stop, restart) and handle server startup / shutdown correctly.
-   **Items**:
    -   Build artifacts
        -   Compiled binaries
        -   Script code
        -   Static assets
    -   Configuration files / environment variables


<a id="orged869b4"></a>

### Custom AMI (Custom OS Images)

A snapshot based on a base OS image, custom-built with required components such
as runtime, dependencies, and service definition, allowing the image to be
reusable with just the application-specific items copied over.

Downsides:

-   Sharing across accounts can be limited or cumbersome in some cloud providers
    (e.g. AWS AMI)
-   Can be tied to a specific OS version
-   Dependencies baked into the image can become outdated and require rebuilds

Custom AMIs shorten the required actions/steps during deployment:

-   **Steps**:
    -   Install dependencies (libraries: OpenSSL)
-   **Items**:
    -   Build artifacts
        -   Compiled binaries
        -   Script code
        -   Static assets
    -   Configuration files / environment variables


<a id="orgd0b0dd6"></a>

## Lambda:

Mostly for stateless workloads, short bursts of work to be done, and with no
long-lived state to persist i.e. endpoint access (read record), RPC function
(quick data manipulation).

Lambda functions require the below actions/components during deployment:

-   Components:
    -   Deployment packages
    -   Runtime dependencies


<a id="org108b5f7"></a>

## Docker (Containerization)

Docker images are typically created in layers, containing files or performing
certain actions:

-   Base image i.e. alpine (minimal), Ubuntu, python:3-alpine, etc.
    -   Similar conceptually to a base AMI for EC2.
-   Source files
    -   Typically some sort of files copied over from the build environment into the
        image through \`COPY\`.
    -   Examples:
        -   Static files
        -   Source code
-   Commands
    -   Commands to run during build steps i.e. \`RUN\`.
    -   Examples:
        -   Install some runtime
        -   (Optional) Compile binary
        -   Install dependencies
-   Metadata
    -   What to run once started
    -   Volumes to mount or use

Once all layers are defined you then have a disk installation &ldquo;image&rdquo; of your
application versioned to a specific time. It&rsquo;s repeatable as it combines all
items you need to run the image pre-configured during build.


<a id="org8e4ebbc"></a>

### Container Engine

Once an image is built, you can deploy multiple copies of your image using a
container engine. Environment variables and configuration files are then
injected into the container when the engine runs the image at runtime.

Containers typically run in isolated environments within an OS. This is the big
difference between virtual machines or EC2 instances:

> Virtual machines are full OS instances and are bulkier, while container images
> are lightweight. To the process inside the container, it appears to run on its
> own machine, but it does not have an entire separate computer. Access to the
> file system, ports, network, and other aspects are controlled by the engine,
> isolating them to their own sandbox unless explicitly allowed to connect to
> other resources..


<a id="org53ab5cc"></a>

### Container Orchestrators (Kubernetes, Swarm)

Once you have container images, orchestrators like Kubernetes or Docker Swarm
manage running those containers across one or more machines.

They typically handle:

-   Scheduling containers onto available nodes
-   Restarting containers if they fail
-   Scaling the number of replicas up or down
-   Service discovery and load balancing between container instances
-   Rolling updates and, in some cases, rollbacks

Orchestrators work with:

-   Container images (built beforehand)
-   Runtime configuration:
    -   Environment variables
    -   Configuration files or mounted volumes
    -   Networking and service definitions

Unlike raw container engines on a single host, orchestrators coordinate
containers across a cluster of machines, providing higher-level abstractions for
availability, scaling, and lifecycle management.

