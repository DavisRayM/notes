
# Table of Contents

1.  [Deployments](#orgad91680)
    1.  [EC2 (Provided OS Images)](#org4471f26)
        1.  [Custom AMI (Custom OS Images)](#org7c667ad)
    2.  [Lambda](#org31559cb)
    3.  [Docker (Containerization)](#org2a8af6d)
        1.  [Container Engine](#org6eb9bb5)
        2.  [Container Orchestrators (Kubernetes, Swarm)](#org20457e6)

ID:       7e302571-321f-4c6f-909b-7417527d9f8d


<a id="orgad91680"></a>

# Deployments


<a id="org4471f26"></a>

## EC2 (Provided OS Images)

Primarily used for workloads that need persistent state or for executing long-running tasks.

Typically, virtual machine services require the following actions during deployment:

-   **Steps**:
    -   Install language runtime (Node.js, JVM) and package manager
    -   Install dependencies (e.g., libraries like OpenSSL)
    -   Create a service definition:
        -   Use a system manager like \`systemd\` to control the service throughout its lifecycle (start, stop, restart) and properly manage server startup and shutdown.
-   **Items**:
    -   Build artifacts
        -   Compiled binaries
        -   Script code
        -   Static assets
    -   Configuration files and environment variables


<a id="org7c667ad"></a>

### Custom AMI (Custom OS Images)

A snapshot based on a base OS image, custom-built with the required components such as runtime, dependencies, and service definitions, allowing for reuse by copying only application-specific items.

Downsides:

-   Sharing across accounts may be limited or cumbersome in some cloud providers (e.g., AWS AMI).
-   Can be tied to a specific OS version.
-   Dependencies baked into the image can become outdated, necessitating rebuilds.

Custom AMIs simplify the deployment process:

-   **Steps**:
    -   Install dependencies (e.g., libraries like OpenSSL)
-   **Items**:
    -   Build artifacts
        -   Compiled binaries
        -   Script code
        -   Static assets
    -   Configuration files and environment variables


<a id="org31559cb"></a>

## Lambda

Ideal for stateless workloads, short bursts of computation, and operations that do not need to maintain long-lived state, such as endpoint access (reading records) or RPC functions (quick data manipulation).

Lambda functions require the following components during deployment:

-   Components:
    -   Deployment packages
    -   Runtime dependencies


<a id="org2a8af6d"></a>

## Docker (Containerization)

Docker images are constructed in layers, containing files or executing specific actions:

-   Base image (e.g., alpine, Ubuntu, python:3-alpine).
    -   Conceptually similar to a base AMI for EC2.
-   Source files
    -   Typically includes files copied from the build environment into the image using \`COPY\`.
    -   Examples:
        -   Static files
        -   Source code
-   Commands
    -   Instructions to run during build steps (e.g., \`RUN\`).
    -   Examples:
        -   Install a runtime
        -   (Optional) Compile binaries
        -   Install dependencies
-   Metadata
    -   Specifies what to execute once the container starts
    -   Defines which volumes to mount

Once all layers are defined, you create a disk installation &ldquo;image&rdquo; of your application versioned to a specific point in time. This process is repeatable, as it encapsulates all items needed for running the image, pre-configured during the build.

The main benefits of Docker include:

-   Reduces pre-deployment steps during building; ensuring a correct filesystem layout and required directories, among other aspects.
-   Facilitates running multiple processes in the same environment while isolating them in their respective sandboxes.
-   Less resource-intensive than emulating a full virtual machine.


<a id="org6eb9bb5"></a>

### Container Engine

After an image is built, you can deploy multiple instances using a container engine. Environment variables and configuration files are injected into the container when the engine executes the image at runtime.

Containers operate in isolated environments within an OS. This is the key distinction from virtual machines or EC2 instances:

> Virtual machines are standalone OS instances and are bulkier, whereas container images are lightweight. From the perspective of a process inside a container, it appears to run on its own machine, but it does not operate on a separate physical computer. Access to the file system, ports, networks, and other resources is controlled by the engine, isolating processes in their respective sandboxes unless explicitly allowed to connect to other resources.


<a id="org20457e6"></a>

### Container Orchestrators (Kubernetes, Swarm)

Once you have container images, orchestrators like Kubernetes or Docker Swarm manage the execution of those containers across multiple machines.

They typically handle:

-   Scheduling containers on available nodes
-   Restarting containers if they fail
-   Scaling the number of replicas up or down
-   Service discovery and load balancing among container instances
-   Performing rolling updates and, if necessary, rollbacks

Orchestrators work with:

-   Pre-built container images
-   Runtime configuration:
    -   Environment variables
    -   Configuration files or mounted volumes
    -   Networking and service definitions

Unlike raw container engines on a single host, orchestrators coordinate containers across a cluster of machines, providing higher-level abstractions for managing containerized applications, including availability, scaling, and lifecycle management.

