- [Floci IO](https://github.com/floci-io/floci)
- [Article 1](https://blog-ocampoge.medium.com/floci-the-lightweight-local-aws-emulator-360d0030f504)

# Floci Architecture Overview

[Floci](https://floci.io/) is a highly efficient, free, and open-source local cloud emulator designed for development, testing, and CI/CD pipelines. It emerged as a prominent, MIT-licensed alternative to LocalStack following the commercialization and archiving of LocalStack's open-source community edition. [1, 2, 3, 4] 

The core architectural goal of Floci is to deliver true emulation rather than mock responses, allowing engineers to test complex cloud infrastructures entirely offline with virtually zero overhead. [5, 6, 7, 8, 9] 

## 🏛️ Floci Three-Layer Architecture
For every cloud service it emulates, the Floci Architecture breaks down into a clean, three-layer system: [10, 11] 

```txt

   [ ☁️ AWS SDK / CLI / Terraform / OpenTofu]
                  │
                  ▼
         ┌─────────────────┐
         │   HTTP Router   │  (Listens on standard port e.g., 4566)
         └────────┬────────┘
                  │
                  ├─────────────────────────┐
                  ▼                         ▼
       ┌─────────────────────┐   ┌─────────────────────┐
       │ In-Process Handlers │   │  Container Services │
       │ (Stateless/Stateful)│   │  (Real Docker Pods) │
       └──────────┬──────────┘   └──────────┬──────────┘
                  ▼                         ▼
         ┌─────────────────┐     ┌─────────────────┐
         │ Memory / Disk   │     │   Docker API    │
         │  Storage Modes  │     └─────────────────┘
         └─────────────────┘
```

### Layer 1. The Routing Layer (HTTP Router | Quarkus Router)

Acts as the entry point that intercepts incoming API traffic from your development tools (like the AWS SDK, CLI, Terraform, or OpenTofu). It routes requests seamlessly by matching wire-protocol signatures.

### Layer 2. The Service Execution Layer

To balance high performance with realistic behavior, Floci splits services into two handling paradigms:

* In-Process Handlers: Lightweight, stateless, or basic stateful services (such as S3, SQS, SNS, DynamoDB, IAM, and STS) run natively inside the core binary. They are built to be extremely fast and wire-protocol compatible.

* Container Services: Complex data and compute engines (such as AWS Lambda, RDS, ElastiCache, ECS, and EKS) do not use mock logic. Instead, Floci uses the Docker API to dynamically orchestrate real, isolated underlying engines (e.g., spawning a true PostgreSQL instance for RDS, or a Redpanda instance for MSK). This guarantees 100% protocol fidelity.

### Layer 3. The Storage & Persistence Layer

Manages data states through four distinct storage modes tailored to specific developer scenarios: Ephemeral In-Memory (great for fast AI sandboxes), Persisted Disk, Hybrid, and Write-Ahead Log (WAL) persistence.

## Architecture Deep-Dive: Dual-Kubernetes Paradigms in Floci & KinD

Understanding the distinction between **Floci's automated k3s orchestration engine** and **standalone KinD clusters** is crucial for local multi-cloud integration.

---

### **Understanding Floci's Native EKS Engine vs. Explicit KinD Architecture**

#### **1. How Floci Handles EKS natively (Implicit k3s)**

When you invoke `aws_eks_cluster` against a standard Floci emulator setup without overriding orchestrator configurations:

* **Host Socket Control:** Because `floci-emulator` mounts `/var/run/docker.sock`, Floci listens to the AWS EKS API wire protocol on port `4566`.


* **Dynamic k3s Container Spawning:** Upon receiving a cluster creation command, Floci automatically calls the host Docker socket to pull and launch a `rancher/k3s` container acting as the EKS control plane.


* **Implicit Lifecycle:** This k3s instance is ephemeral—managed automatically inside Docker network bridges tied directly to Floci's internal lifecycle.

#### **2. Why This Blueprint Explicitly Prefers KinD**

While Floci's background k3s container works for lightweight API checks, this repository uses an **explicitly configured KinD cluster** for the following reasons:

* **Multi-Node Topologies:** KinD allows declarative creation of dedicated control planes and distinct worker nodes (`local-kubernetes/kind-config.yaml`), whereas Floci defaults to single-node k3s instances.


* **Network & Ingress Predictability:** KinD exposes static host ports (`80`, `443`) bound directly to host interfaces for ingress traffic routing.


* **Decoupled Execution:** Keeping KinD independent prevents accidental cluster destruction when restarting or tearing down Floci cloud stacks.

## ⚡ Key Floci Architectural Capabilities & Tech Stack

```mermaid
flowchart LR
    Client["AWS SDK / CLI / Terraform / OpenTofu"]

    subgraph Floci ["Floci, port 4566"]
        Router["HTTP/Quarkus Router\nJAX-RS / Vert.x"]

        subgraph Stateless ["Stateless Services"]
            A["SSM · SQS · SNS\nIAM · STS · KMS\nSecrets Manager · SES\nCognito · Kinesis\nEventBridge · Scheduler · AppConfig\nCloudWatch · Step Functions\nCloudFormation · ACM · Config · CloudTrail\nAPI Gateway · AppSync · ELB v2 · Auto Scaling\nElastic Beanstalk · CodeDeploy · CodePipeline · Backup · Bedrock Runtime · Route53 · Transfer"]
        end

        subgraph Stateful ["Stateful Services"]
            B["S3 · DynamoDB\nDynamoDB Streams"]
        end

        subgraph Containers ["Container Services"]
            C["Lambda\nElastiCache\nRDS\nNeptune\nECS\nEC2\nMSK\nEKS\nOpenSearch\nCodeBuild\nManaged Flink"]
            D["Athena -> floci-duck\nDuckDB sidecar"]
        end

        Router --> Stateless
        Router --> Stateful
        Router --> Containers
        Stateless & Stateful --> Store[("StorageBackend\nmemory · hybrid · persistent · wal")]
    end

    Docker["Docker Engine"]
    Client -->|"HTTP :4566\nAWS wire protocol"| Router
    Containers -->|"Docker API\nIAM / SigV4 auth"| Docker
```


### Quarkus and GraalVM Native Image

Floci is built on the [Quarkus framework and Vert.x](https://www.youtube.com/watch?v=wQVQHuJ0FC0), and compiled into native binaries using GraalVM Mandrel. This compilation strategy achieves an under 24 ms startup time and a tiny 13 MB idle memory footprint, making it heavily optimized for resource-constrained developer laptops and fast CI runners.

### Multi-Cloud Emulation Architecture

While it began as a drop-in AWS alternative running on port 4566, the ecosystem quickly expanded into distinct, lightweight binaries for other major providers:
* **Floci (AWS):** Emulates over 75 AWS services.
* **Floci-AZ (Azure):** Emulates Cosmos DB, Blob Storage, AKS, and Functions.
* **Floci-GCP (Google Cloud):** Uses advanced ALPN negotiation to serve all gRPC and REST traffic over a single port (4588).

### Zero-Friction Operations

Unlike other corporate tools, Floci’s architecture requires no authentication tokens, no internet connections, and no feature gates. Every enterprise-grade feature is unlocked out-of-the-box.

# Q. Why use Floci?

| Use Case                        | Why It Matters                                                              |
|---------------------------------|-----------------------------------------------------------------------------|
| Local cloud development         | Develop against real AWS APIs without network calls, cost, or account setup |
| CI/CD pipeline testing          | Run integration tests in ephemeral CI runners without AWS credentials       |
| Terraform / OpenTofu validation | Validate IaC plans and applies locally before touching real infrastructure  |
| Lambda experimentation          | Iterate on Lambda functions in milliseconds vs. the 10-second deploy cycle  |
| Offline development             | Work on flights, trains, or anywhere without internet                       |

> Floci makes all the above use cases painless. No sign-ups, no API keys, no telemetry. Pull the image and build.

# Q. How does Floci handles Amazon MongoDB/DocumentDB, ElasticCache, EKS and EC2 services locally?

Floci can natively handle all four of these services locally

 Floci uses a "Real Docker Backend" architecture. Instead of relying on shallow API mocks for complex infrastructure, Floci leverages your machine's local Docker socket to orchestrate real, fully functional underlying open-source engines. [1, 2, 3, 4, 5] 
The specific mechanisms for how Floci handles each service locally provide a blueprint for configuration.

# Q. How does Floci emulates Amazon MongoDB (DocumentDB)?

When you issue a command to create an Amazon DocumentDB cluster, Floci automatically spins up a real, isolated Docker container running the official MongoDB engine backend. [1] 

* The Benefit: Your system gets actual DocumentDB wire protocol and query compatibility without requiring an abstract mock translation layer. [1, 2] 

# Q. How does Floci emulates Amazon ElastiCache?

Floci intercepts the ElastiCache API call and automatically provisions a real Redis container.

* The Benefit: It acts as a SigV4 proxy. If your application enforces AWS IAM database authentication to connect to ElastiCache, Floci intercepts and translates those secure AWS requests directly into the standard RESP (Redis) protocol. [6, 7] 

# Q. How does Floci emulates Amazon EC2?

When your application calls RunInstances, Floci spawns a standard Docker container to mimic an EC2 instance.

* The Benefit: It automatically injects your requested SSH keys, executes your base64-encoded UserData scripts upon boot, and provides a functioning internal Instance Metadata Service (IMDSv1 & IMDSv2). This ensures code inside the "VM" can fetch mock IAM instance profile credentials natively. [8] 

# Q. How does Floci emulates Amazon Elastic Kubernetes Service (EKS)?

Floci features a native EKS engine. When you issue an `aws eks create-cluster` command or apply an `aws_eks_cluster` Terraform resource targeting Floci, Floci will automatically access the host's `/var/run/docker.sock` to launch an internal `rancher/k3s` Docker container. This serves as a lightweight, real Kubernetes control plane for Floci's EKS emulator.

Executing CreateCluster instructs Floci to deploy an actual, lightweight k3s (Kubernetes) cluster inside a nested container environment. [8, 9] 

* The Benefit: It outputs standard kubeconfig data, allowing you to point your native local kubectl or Helm clients directly at it to run actual deployments, pods, and ingress routing loops. [8, 9, 10] 

# Q. What is the need of having K8S Dualism in this Repository?

While Floci automatically manages a **k3s container** behind the scenes to emulate EKS, our active development environment should route all real container workloads, ingress rules, and `kubectl` contexts to the **KinD matrix**. Both clusters run in parallel inside separate Docker container namespaces attached to the `local-aws-net` bridge network.

1. **Floci-Managed k3s Engine:** Running `terraform apply` provisions an `aws_eks_cluster` resource (`micro-eks`). Floci handles this by spawning an implicit k3s container running on Floci's internal container subnets. This k3s instance exists strictly to satisfy AWS API fidelity and direct `aws eks` orchestration tests.

2. **Explicit KinD Cluster:** The primary cluster used for application workloads, local deployments (`kubectl`), and NGINX Ingress routing in this environment is the **KinD cluster** (`local-eks`), explicitly built via `setup.sh`.

# Q. What is a crucial Pre-requisite for KinD / Multi-Container Environments

Because Floci needs to orchestrate these containers dynamically, you must mount your host system's Docker socket directly into the Floci image: [5] 

```yaml
volumes:
  - /var/run/docker.sock:/var/run/docker.sock # 👈 Absolute requirement for EKS, EC2, and ElastiCache
```

### Ref:
- [1] [https://github.com](https://github.com/floci-io/floci)
- [2] [https://blog-ocampoge.medium.com](https://blog-ocampoge.medium.com/floci-the-lightweight-local-aws-emulator-360d0030f504)
- [3] [https://www.reddit.com](https://www.reddit.com/r/aws/comments/1t1bomf/floci_now_runs_ec2_ecs_eks_and_codebuild_as_real/)
- [4] [https://fossunited.org](https://fossunited.org/c/mangalore/foss-meetup-june-2026/cfp/dj78oi5n5v)
- [5] https://floci.io
- [6] [https://floci.io](https://floci.io/aws/)
- [7] [https://dev.to](https://dev.to/nahuel990/localstack-is-dead-ministack-runs-real-databases-for-free-1lim)
- [8] [https://www.reddit.com](https://www.reddit.com/r/aws/comments/1t1bomf/floci_now_runs_ec2_ecs_eks_and_codebuild_as_real/)
- [9] [https://www.neteye-blog.com](https://www.neteye-blog.com/blog/2026/07/03/building-a-local-aws-dev-environment-with-floci-part-1-eks-and-ecr/)
- [10] [https://www.dennisokeeffe.com](https://www.dennisokeeffe.com/blog/2019-05-02-eks-basics)
