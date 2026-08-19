This optimized configuration bundles the entire environment into two independent Docker Compose stacks and one master automation script (setup.sh).

The Terraform Provisioner remains completely isolated inside its own separate Docker Compose file. This allows you to orchestrate the core infrastructure platforms seamlessly, while triggering, modifying, or scaling your Terraform infrastructure declaratively at will, without ever restarting the underlying cloud or Kubernetes services.

## 🚀 The Master AWS & KinD Integration Blueprint

### 📂 Unified Folder Structure

Create a root folder named local-cloud-env with the following sub-directories and files:

```sh
local-cloud-env/
├── backend-infra/
│   └── docker-compose.yml       # Stack 1: Controls the Floci Core
├── local-kubernetes/
│   └── kind-config.yaml         # High-Fidelity multi-node KinD definitions
├── terraform-provisioner/
│   ├── docker-compose.yml       # Stack 2: Automated IaC Execution Layer
│   ├── provider.tf              # AWS API Redirection Settings
│   └── main.tf                  # Declarative Cloud Architecture Blueprint
├── setup.sh                 # [EXECUTE THIS] Starts the entire framework
└── teardown.sh              # [EXECUTE THIS] Destroys and cleans up everything
```

## 💾 1. The Core Infrastructure Configurations

### 📄 backend-infra/docker-compose.yml (Stack 1)

This file defines the core Floci Cloud Engine and pre-configures a shared bridge network (local-aws-net) so that all future KinD nodes and Terraform containers can intercommunicate.

```yml
version: '3.8'
services:
  floci:
    image: floci/floci:latest
    container_name: floci-emulator
    ports:
      - "4566:4566"
    volumes:
      # Allows Floci to dynamically orchestrate real database engine 
      # and EC2 container workloads using your host system socket
      - /var/run/docker.sock:/var/run/docker.sock
    networks:
      - env-net
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:4566/health"]
      interval: 3s
      timeout: 2s
      retries: 5
networks:
  env-net:
    name: local-aws-net
    driver: bridge
```


### 📄 local-kubernetes/kind-config.yaml

```yml
kind: ClusterapiVersion: kind.x-k8s.io/v1alpha4nodes:
- role: control-plane
  kubeadmConfigPatches:
  - |
    kind: InitConfiguration
    nodeRegistration:
      kubeletExtraArgs:
        node-labels: "ingress-ready=true"  extraPortMappings:
  - containerPort: 80
    hostPort: 80
    protocol: TCP
  - containerPort: 443
    hostPort: 443
    protocol: TCP
- role: worker
- role: worker
```

### 📄 terraform-provisioner/docker-compose.yml (Stack 2)

This standalone container mounts your local .tf configuration workspace, hooks into the shared bridge network, applies your architecture, and immediately terminates upon success.

```yml
version: '3.8'
networks:
  local-aws-net:
    external: true
services:
  terraform:
    image: hashicorp/terraform:1.6.0
    container_name: local-terraform-runner
    volumes:
      - .:/infra
    working_dir: /infra
    networks:
      - local-aws-net
    environment:
      - AWS_ACCESS_KEY_ID=mock-key
      - AWS_SECRET_ACCESS_KEY=mock-secret
      - AWS_DEFAULT_REGION=us-east-1
    entrypoint: /bin/sh -c
    command: >
      "
      echo '🏁 Step 1: Initializing local Terraform providers...';
      terraform init && \
      echo '🚀 Step 2: Deploying your six AWS services to Floci...';
      terraform apply -auto-approve
      "
```

### 📄 terraform-provisioner/provider.tf

```hcl
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.0"
    }
  }
}

provider "aws" {
  region                      = "us-east-1"
  access_key                  = "mock-key"
  secret_key                  = "mock-secret"
  skip_credentials_validation = true
  skip_metadata_api_check     = true
  skip_requesting_account_id  = true

  endpoints {
    s3          = "http://floci-emulator:4566"
    kms         = "http://floci-emulator:4566"
    docdb       = "http://floci-emulator:4566"
    elasticache = "http://floci-emulator:4566"
    ec2         = "http://floci-emulator:4566"
    eks         = "http://floci-emulator:4566"
  }
}
```

### 📄 terraform-provisioner/main.tf

```hcl
resource "aws_docdb_cluster" "local_mongo" {
  cluster_identifier  = "local-mongo-cluster"
  engine              = "docdb"
  master_username     = "admin"
  master_password     = "password123"
  skip_final_snapshot = true
}

resource "aws_elasticache_cluster" "local_cache" {
  cluster_id           = "local-cache"
  engine               = "redis"
  node_type            = "cache.t3.micro"
  num_cache_nodes      = 1
  parameter_group_name = "default.redis7"
  port                 = 6379
}

resource "aws_s3_bucket" "local_storage" {
  bucket        = "application-assets"
  force_destroy = true
}

resource "aws_kms_key" "local_key" {
  description             = "Default Local Application Key"
  deletion_window_in_days = 7
}

resource "aws_eks_cluster" "local_eks" {
  name     = "micro-eks"
  role_arn = "arn:aws:iam::000000000000:role/mock-eks-role"
  vpc_config {
    subnet_ids = ["subnet-00000000", "subnet-11111111"]
  }
}

resource "aws_instance" "local_compute" {
  ami           = "ami-mock"
  instance_type = "t3.micro"
  tags = { Name = "LocalComputeNode" }
}
```

------------------------------
## ⚙️ 2. The Master Automation Setup Script

### setup.sh

This master orchestrator weaves all isolated commands into a single sequence. It launches Stack 1, spins up your multi-node KinD cluster, links the container network switches, configures the NGINX Ingress system, and waits for your confirmation.

```bash
#!/bin/bashset -e

echo "🚀 Commencing unified environment orchestration..."

echo "🔹 [1/4] Booting Core Platform Cloud Engine (Stack 1)..."
cd backend-infra && docker compose up -d && cd ..

echo "🔹 [2/4] Constructing High-Fidelity KinD Cluster Matrix..."
kind create cluster --name local-eks --config local-kubernetes/kind-config.yaml

echo "🔹 [3/4] Blending Virtual Kubernetes Nodes into the Host AWS Virtual Switch..."
docker network connect local-aws-net local-eks-control-plane
docker network connect local-aws-net local-eks-worker
docker network connect local-aws-net local-eks-worker2

echo "🔹 [4/4] Deploying NGINX Ingress Routing Platform inside KinD..."
kubectl apply -f https://githubusercontent.com

echo "⏳ Waiting for Ingress controller readiness parameters..."
kubectl wait --namespace ingress-nginx \
  --for=condition=ready pod \
  --selector=app.kubernetes.io/component=controller \
  --timeout=90s

echo "✨ Base Environment is operational! Run Stack 2 to apply your infrastructure blueprints."
```

Make it executable and launch it:

```bash
chmod +x setup.sh
./setup.sh
```

Once the base system finishes initializing, execute Stack 2 to let the isolated Terraform engine provision your resources seamlessly:

```bash
cd terraform-provisioner && docker compose up && cd ..
```

------------------------------
## 🧪 3. Complete AWS Service Testing Guide

To verify each of the 6 simulated services, run these test commands from your host machine terminal.

### 🔧 Prep your host environment parameters:

```bash
export AWS_ACCESS_KEY_ID=mock-key
export AWS_SECRET_ACCESS_KEY=mock-secret
export AWS_DEFAULT_REGION=us-east-1
export AWS_ENDPOINT_URL=http://localhost:4566
```

### Test 1: Amazon MongoDB (DocumentDB)
Verify that the cluster control plane exists and matches the architecture defined in Terraform:

```bash
aws docdb describe-db-clusters --db-cluster-identifier local-mongo-cluster
```

### Test 2: Amazon ElastiCache
Confirm that Floci has successfully provisioned a real underlying Redis engine tracking layer:

```bash
aws elasticache describe-cache-clusters --cache-cluster-id local-cache
```

### Test 3: Amazon S3
Create a temporary dummy file and upload it directly into your local S3 object container:

```bash
echo "Testing local object store" > sample.txt
aws s3 cp sample.txt s3://application-assets/
aws s3 ls s3://application-assets/
```

### Test 4: KMS Keys
List the encryption elements to confirm your cryptographic control plane keys are active:

```bash
aws kms list-keys
```

### Test 5: Amazon EKS
Query the cluster metadata parameters to confirm EKS control plane mapping validation:

```bash
aws eks describe-cluster --name micro-eks
```

### Test 6: Amazon EC2
Inspect your EC2 instances to verify that the proxy virtual machine container is online and tagged properly:

```bash
aws ec2 describe-instances --filters "Name=tag:Name,Values=LocalComputeNode"
```

## 🧹 4. Complete Automated Cleanup Framework

### teardown.sh
This script uses your isolated Terraform Compose configuration to tear down the cloud resources, destroys the KinD cluster, halts the background emulator, and cleans up any dynamic container sidecars.

```bash
#!/bin/bash
echo "⚠️ Commencing complete environment teardown..."

echo "🛑 Destroying declarative Terraform managed resources (Stack 2)..."
cd terraform-provisioner
docker compose run --rm terraform /bin/sh -c "terraform destroy -auto-approve" || true
cd ..

echo "🛑 Obliterating the KinD Kubernetes cluster..."
kind delete cluster --name local-eks || true

echo "🛑 Halting Floci Core platform engines and associated volumes (Stack 1)..."
cd backend-infra && docker compose down -v && cd ..

echo "🛑 Cleaning up orphaned runtime container processes from host engine socket..."
docker ps -a --filter "name=floci-" -q | xargs -r docker rm -f || true

echo "✨ Teardown clean complete. Machine is completely reset."
```

Make it executable and execute it whenever you need a fresh slate:

```bash
chmod +x teardown.sh
./teardown.sh
```

Would you like to explore how to bundle a Kubernetes testing deployment into the setup.sh script to verify that inner pods can resolve these local AWS resources automatically upon boot?

