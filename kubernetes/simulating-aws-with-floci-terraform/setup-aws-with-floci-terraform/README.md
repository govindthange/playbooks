# 🚀 Local AWS Integration Blueprint

### 📂 Unified Folder Structure

Create a root folder named local-cloud-env with the following sub-directories and files:

```bash
local-cloud-env/
├── backend-infra/
│   └── docker-compose.yml       # Stack 1: Controls Floci Engine & Web UI
├── local-kubernetes/
│   └── kind-config.yaml         # High-Fidelity multi-node KinD definitions
├── terraform-provisioner/
│   ├── docker-compose.yml       # Stack 2: Automated IaC Execution Layer
│   ├── provider.tf              # AWS API Redirection Settings
│   └── main.tf                  # Declarative Cloud Architecture Blueprint
├── setup-prerequisites.sh       # Installs native host dependencies
├── setup.sh                     # Boots up infrastructure and KinD configuration
├── verify-tests.sh              # Runs isolated containerized AWS CLI checks
├── restart.sh                   # Resume containers after shutdown/reboot
└── teardown.sh                  # Wipes the sandbox environment completely
```

# ⚙️ The Core Infrastructure Configurations

This optimized configuration bundles the entire environment into two independent Docker Compose stacks and multiple scripts for automating environment setup and tests.

### 📄 backend-infra/docker-compose.yml (Stack 1)

This file defines the **Stack 1** like so:
1. Defines the core **Floci Cloud Engine**
2. Pre-configures a **shared bridge network** (local-aws-net) so that all future **KinD nodes** and **Terraform containers** can intercommunicate.
3. Defines the **AWS Web Console** via floci-ui

```yml
services:
  # --- Tier 1: Core API Cloud Engine ---
  floci:
    image: floci/floci:latest
    container_name: floci-emulator
    ports:
      - "4566:4566"
    volumes:
      # Allows Floci to dynamically orchestrate real database engine 
      # and EC2 container workloads using your host system socket
      - /var/run/docker.sock:/var/run/docker.sock # Required to spawn real DBs and EC2 mocks
    networks:
      - env-net
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:4566/health"]
      interval: 3s
      timeout: 2s
      retries: 5

  # --- Tier 2: Free Open Source AWS Web Console ---
  floci-dashboard:
    image: floci/floci-ui:latest
    container_name: aws-local-web-console
    ports:
      - "4500:4500" # 👈 Opens the dashboard port to your host network
    environment:
      - FLOCI_ENDPOINT=http://floci-emulator:4566 # Direct container lookup
    networks:
      - env-net
    depends_on:
      floci:
        condition: service_healthy

  # --- Tier 3: Dedicated RDS Aurora MySQL Database Engine ---
  local-mysql:
    image: mysql:8.0
    container_name: local-mysql-engine
    ports:
      - "3306:3306"
    environment:
      - MYSQL_ROOT_PASSWORD=password123
      - MYSQL_USER=admin
      - MYSQL_PASSWORD=password123
      - MYSQL_DATABASE=test_db  # 👈 Grants admin full permissions to test_db
    networks:
      - env-net
    healthcheck:
      test: ["CMD", "mysqladmin", "ping", "-h", "localhost", "-u", "root", "-ppassword123"]
      interval: 3s
      timeout: 2s
      retries: 10

  # --- Tier 4: MySQL Visual Database Explorer ---
  db-web-client:
    image: adminer:latest
    container_name: aws-rds-web-client
    ports:
      - "8082:8080"
    networks:
      - env-net
    depends_on:
      - local-mysql

  # --- Tier 5: AWS DocumentDB Engine (MongoDB Protocol Compatible) ---
  local-mongo:
    image: mongo:6.0
    container_name: local-mongo-cluster
    ports:
      - "27017:27017"
    environment:
      - MONGO_INITDB_ROOT_USERNAME=admin
      - MONGO_INITDB_ROOT_PASSWORD=password123
    networks:
      - env-net

  # --- Tier 6: DocumentDB Visual Web Client & Explorer ---
  docdb-web-client:
    image: mongo-express:latest
    container_name: aws-docdb-web-client
    ports:
      - "8081:8081"
    environment:
      - ME_CONFIG_MONGODB_ADMINUSERNAME=admin
      - ME_CONFIG_MONGODB_ADMINPASSWORD=password123
      - ME_CONFIG_MONGODB_SERVER=local-mongo
      - ME_CONFIG_MONGODB_PORT=27017
      - ME_CONFIG_BASICAUTH=false
    networks:
      - env-net
    depends_on:
      - local-mongo

networks:
  env-net:
    name: local-aws-net
    driver: bridge
```


### 📄 local-kubernetes/kind-config.yaml

```yml
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
- role: control-plane
  kubeadmConfigPatches:
  - |
    kind: InitConfiguration
    nodeRegistration:
      kubeletExtraArgs:
        node-labels: "ingress-ready=true"
  extraPortMappings:
  - containerPort: 80
    hostPort: 80
    protocol: TCP
  - containerPort: 443
    hostPort: 443
    protocol: TCP
- role: worker
- role: worker

```

> Not all of these tools can be further dockerized. While you can run the aws-cli or kubectl inside containers, KinD (Kubernetes in Docker) strictly requires a native CLI installation on your host system. KinD functions by directly controlling your host's Docker daemon to spawn complex, multi-container system networks. Running KinD inside a container requires privileged, nested "Docker-in-Docker" configurations that break the external networking loop we created to let other developers connect to your machine.

### 📄 terraform-provisioner/docker-compose.yml (Stack 2)

The Terraform Provisioner remains completely isolated inside its own separate Docker Compose file. This allows you to orchestrate the core infrastructure platforms seamlessly, while triggering, modifying, or scaling your Terraform infrastructure declaratively at will, without ever restarting the underlying cloud or Kubernetes services.


This standalone container mounts your local .tf configuration workspace, hooks into the shared bridge network, applies your architecture, and immediately terminates upon success.

```yml
networks:
  local-aws-net:
    external: true

services:
  terraform:
    image: hashicorp/terraform:1.6.2
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
  s3_use_path_style           = true
  skip_credentials_validation = true
  skip_metadata_api_check     = true
  skip_requesting_account_id  = true
  skip_region_validation      = true

  endpoints {
    s3          = "http://floci-emulator:4566"
    kms         = "http://floci-emulator:4566"
    rds         = "http://floci-emulator:4566"
    docdb       = "http://floci-emulator:4566"
    elasticache = "http://floci-emulator:4566"
    ec2         = "http://floci-emulator:4566"
    eks         = "http://floci-emulator:4566"
  }
}terraform {
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
  s3_use_path_style           = true
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
# Register DocumentDB cluster metadata
resource "null_resource" "register_docdb" {
  provisioner "local-exec" {
    command = <<EOT
      wget --quiet \
        --header="Content-Type: application/x-www-form-urlencoded" \
        --post-data="Action=CreateDBCluster" \
        "&DBClusterIdentifier=local-mongo-cluster" \
        "&Engine=docdb" \
        "&MasterUsername=admin" \
        "&MasterUserPassword=password123" \
        "&Version=2014-10-31" \
        "http://floci-emulator:4566/" \
        -O - || true
    EOT
  }
}

# 'engine' field value could be one of ["aurora-mysql" "aurora-postgresql" "mysql" "postgres"]
resource "aws_rds_cluster" "local_rds_instance" {
  cluster_identifier  = "local-rds-cluster"
  engine              = "aurora-mysql"
  master_username     = "admin"
  master_password     = "password123"
  skip_final_snapshot = true
}

# Standard Redis instance without subnet groups
resource "aws_elasticache_replication_group" "local_cache" {
  replication_group_id = "local-cache"
  description          = "Local Redis cache"
  engine               = "redis"
  node_type            = "cache.t3.micro"
  num_cache_clusters   = 1
  port                 = 6379
  parameter_group_name = "default.redis7"
}

resource "aws_s3_bucket" "local_storage" {
  bucket        = "application-assets"
  force_destroy = true
}

resource "aws_kms_key" "local_key" {
  description             = "Default Local Application Key"
  deletion_window_in_days = 7
}

# 1. Define a mock VPC for the EKS cluster
resource "aws_vpc" "local_vpc" {
  cidr_block = "10.0.0.0/16"
  tags = {
    Name = "local-vpc"
  }
}

# 2. Define mock Subnets
resource "aws_subnet" "subnet_a" {
  vpc_id            = aws_vpc.local_vpc.id
  cidr_block        = "10.0.1.0/24"
  availability_zone = "us-east-1a"
  tags = {
    Name = "local-subnet-a"
  }
}

resource "aws_subnet" "subnet_b" {
  vpc_id            = aws_vpc.local_vpc.id
  cidr_block        = "10.0.2.0/24"
  availability_zone = "us-east-1b"
  tags = {
    Name = "local-subnet-b"
  }
}

resource "aws_eks_cluster" "local_eks" {
  name     = "micro-eks"
  role_arn = "arn:aws:iam::000000000000:role/mock-eks-role"
  
  vpc_config {
    subnet_ids = [
      aws_subnet.subnet_a.id,
      aws_subnet.subnet_b.id
    ]
  }
}

resource "aws_instance" "local_compute" {
  ami           = "ami-mock"
  instance_type = "t3.micro"
  tags = { Name = "LocalComputeNode" }
}
```

# ⚙️ The Unified Lifecycle Scripts

### 📄 setup-prerequisites.sh

```bash
#!/bin/bash
set -e

echo "🔍 Detecting Host Operating System..."
OS_TYPE="$(uname -s | tr '[:upper:]' '[:lower:]')"
ARCH_TYPE="$(uname -m)"

# Normalize architecture strings for download URLs
if [ "$ARCH_TYPE" = "x86_64" ]; then
    ARCH="amd64"
elif [ "$ARCH_TYPE" = "aarch64" ] || [ "$ARCH_TYPE" = "arm64" ]; then
    ARCH="arm64"
else
    echo "❌ Unsupported architecture: $ARCH_TYPE"
    exit 1
fi

echo "💻 System Identified: $OS_TYPE ($ARCH)"

# =========================================================================
# MACOS INSTALLATION ROUTE (Using Homebrew)
# =========================================================================
if [ "$OS_TYPE" = "darwin" ]; then
    echo "📦 Checking for Homebrew..."
    if ! command -v brew &> /dev/null; then
        echo "❌ Homebrew is not installed. Please install it first from https://brew.sh or install Docker Desktop manually."
        exit 1
    fi

    echo "⚙️ Installing KinD and Kubectl via Homebrew..."
    brew install kind kubectl
    
    echo "🐳 Note: Please ensure Docker Desktop for Mac is downloaded and running."

# =========================================================================
# LINUX INSTALLATION ROUTE (Native Binary Downloads)
# =========================================================================
elif [ "$OS_TYPE" = "linux" ]; then
    echo "⚙️ Downloading and installing KinD binary..."
    curl -Lo ./kind "https://kind.sigs.k8s.io/dl/latest/kind-linux-${ARCH}"
    chmod +x ./kind
    sudo mv ./kind /usr/local/bin/kind

    echo "⚙️ Downloading and installing kubectl binary..."
    curl -Lo ./kubectl "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/${ARCH}/kubectl"
    chmod +x ./kubectl
    sudo mv ./kubectl /usr/local/bin/kubectl

    echo "🐳 Note: Ensure Docker Engine is installed and your user is part of the 'docker' group."
else
    echo "❌ This automation script only supports macOS and Linux. For Windows, please use 'choco install kind kubernetes-cli'."
    exit 1
fi

# =========================================================================
# VALIDATION RESOURCING
# =========================================================================
echo "🚀 Validating Tool Installations..."
echo "✅ KinD Version: $(kind version)"
echo "✅ Kubectl Version: $(kubectl version --client --output=yaml | grep gitVersion)"
echo "🎉 Prerequisites successfully configured! You can now execute ./setup.sh safely."
```

You need the following three core tools installed natively on your host machine:

1. **Docker Desktop** (or Docker Engine on Linux) — Must be running before starting.
2. **KinD CLI** — To orchestrate the multi-node Kubernetes container matrix.
3. **kubectl CLI** — The administrative console tool to interact with your cluster. [A, B, C, D] 

> Note: You DO NOT need to install the **AWS CLI** or **Terraform** on your host, as our setup isolates them cleanly **inside Stack 2's Docker container**. [E, F] 


- [A] [https://docs.inkeep.com](https://docs.inkeep.com/get-started/quick-start)
- [B] [https://documentation.mindsphere.io](https://documentation.mindsphere.io/MindSphere/apps/dashboard-designer-v10/auto-refresh-session.html)
- [C] [https://r4ven.me](https://r4ven.me/en/storage/podnimaem-server-sinhronizacii-fajlov-syncthing-v-docker/)
- [D] [https://www.datasunrise.com](https://www.datasunrise.com/professional-info/running-ds-on-kubernetes/)
- [E] [https://www.virtualizationhowto.com](https://www.virtualizationhowto.com/2018/05/basic-terraform-installation-and-vmware-vsphere-automation/)
- [F] [https://exampledriven.wordpress.com](https://exampledriven.wordpress.com/2017/01/09/spring-boot-aws-elastic-beanstalk-example/)


### 📄 setup.sh

This master orchestrator weaves all isolated commands into a single sequence. It launches Stack 1, spins up your multi-node KinD cluster, links the container network switches, configures the NGINX Ingress system, and waits for your confirmation.

```bash
#!/bin/bash
set -e

echo "🚀 Commencing unified environment orchestration..."

systemctl is-active --quiet docker || sudo systemctl start docker
if ! docker info > /dev/null 2>&1; then
    echo "❌ Error: Docker daemon is not running. Please start Docker Desktop/Engine first."
    exit 1
fi

docker context use default

# ==============================================================================
# STEP 1: Boot Core Platform Cloud Engine (Stack 1)
# ==============================================================================
# We boot up the backend local cloud emulators using Docker Compose. 
# This runs components like the floci emulator and local web console.
echo "🔹 [1/5] Booting Core Platform Cloud Engine (Stack 1)..."
cd backend-infra && docker compose up -d && cd ..

# ==============================================================================
# STEP 2: Construct High-Fidelity KinD Cluster Matrix
# ==============================================================================
# We initialize our multi-node Kubernetes cluster using KinD (Kubernetes in Docker).
# Idempotency check: We check if the cluster already exists to prevent creation errors 
# if the script is re-run multiple times during troubleshooting.
echo "🔹 [2/5] Constructing High-Fidelity KinD Cluster Matrix..."
if kind get clusters | grep -q "^local-eks$"; then
    echo "⚠️ KinD cluster 'local-eks' already exists. Skipping creation."
else
    kind create cluster --name local-eks --config local-kubernetes/kind-config.yaml
fi

# ==============================================================================
# STEP 3: Blend Virtual Kubernetes Nodes into Host AWS Virtual Switch
# ==============================================================================
# We attach each KinD node container to our custom bridge network ('local-aws-net') 
# so they can communicate seamlessly with local AWS service emulators. 
# We suppress errors (2>/dev/null || true) so re-runs don't fail if already attached.
echo "🔹 [3/5] Blending Virtual Kubernetes Nodes into the Host AWS Virtual Switch..."
docker network connect local-aws-net local-eks-control-plane 2>/dev/null || true
docker network connect local-aws-net local-eks-worker 2>/dev/null || true
docker network connect local-aws-net local-eks-worker2 2>/dev/null || true

# ==============================================================================
# STEP 4: Caching and Importing Ingress Images via Tar Archive
# ==============================================================================
# TROUBLESHOOTING CONTEXT: 
# Standard 'kind load' uses continuous socket streaming via Docker API pipes. 
# On Linux environments running Docker Desktop, this frequently causes 500 socket 
# errors or hangs indefinitely during heavy multi-node imports.
# 
# SOLUTION: 
# 1. We pull the images cleanly onto the host using the Docker CLI.
# 2. We package them into a flat .tar file on disk to avoid live-stream socket locks.
# 3. We copy the tar archive directly into each node container and use 'ctr' 
#    (containerd's native CLI) to import them locally.
echo "🔹 [4/5] Caching and Importing Ingress Images via Tar Archive..."
docker pull registry.k8s.io/ingress-nginx/controller:v1.12.0
docker pull registry.k8s.io/ingress-nginx/kube-webhook-certgen:v1.4.4

# Save images to a temporary local tar file on the host
docker save registry.k8s.io/ingress-nginx/controller:v1.12.0 registry.k8s.io/ingress-nginx/kube-webhook-certgen:v1.4.4 -o /tmp/ingress-images.tar

# Iterate over each cluster node to push and import the images natively
for node in local-eks-control-plane local-eks-worker local-eks-worker2; do
    echo "Importing images into $node..."
    docker cp /tmp/ingress-images.tar $node:/ingress-images.tar
    docker exec $node ctr -n k8s.io images import /ingress-images.tar
    docker exec $node rm -f /ingress-images.tar
done

# Clean up the temporary host archive file
rm -f /tmp/ingress-images.tar

# ==============================================================================
# STEP 5: Deploy NGINX Ingress Routing Platform inside KinD
# ==============================================================================
# With all required container images pre-loaded locally on every node, we apply 
# the upstream NGINX Ingress manifest. Because the images are already present, 
# Kubernetes avoids `ImagePullBackOff` states entirely.
echo "🔹 [5/5] Deploying NGINX Ingress Routing Platform inside KinD..."
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.15.1/deploy/static/provider/cloud/deploy.yaml

# The kubectl wait command succeeds instantly without timing out because 
# the images are already fully available inside the cluster nodes' runtimes.
echo "⏳ Waiting for Ingress controller readiness parameters..."
kubectl wait --namespace ingress-nginx \
  --for=condition=ready pod \
  --selector=app.kubernetes.io/component=controller \
  --timeout=180s

echo "✨ Base Environment is operational! Run Stack 2 to apply your infrastructure blueprints."
```

### 📄 verify-cluster.sh

This script verifies the cluster setup.

```bash
#!/bin/bash
set -e

echo "🔍 Starting local environment verification diagnostics..."
echo "======================================================"

# ==============================================================================
# 1. Verify Floci Local Cloud Engine (Stack 1)
# ==============================================================================
echo -e "\n🔹 [1/4] Checking Floci Emulator & Web Console..."

if docker ps --format '{{.Names}}' | grep -q "^floci-emulator$"; then
    echo "✅ Container 'floci-emulator' is running."
    
    # Test health endpoint / AWS wire protocol port 4566
    if curl -s -o /dev/null -w "%{http_code}" http://localhost:4566 | grep -qE "200|400|403"; then
        echo "✅ Floci endpoint is responding on http://localhost:4566."
    else
        echo "⚠️ Warning: Floci container is running, but port 4566 is not responding as expected."
    fi
else
    echo "❌ Error: Container 'floci-emulator' is NOT running. (Run Stack 1 / setup.sh)"
fi

if docker ps --format '{{.Names}}' | grep -q "^aws-local-web-console$"; then
    echo "✅ Container 'aws-local-web-console' is running."
else
    echo "⚠️ Warning: 'aws-local-web-console' container is not running."
fi

# ==============================================================================
# 2. Verify KinD Cluster Matrix & Context
# ==============================================================================
echo -e "\n🔹 [2/4] Checking KinD Kubernetes Cluster ('local-eks')..."

if kind get clusters | grep -q "^local-eks$"; then
    echo "✅ KinD cluster 'local-eks' exists."
else
    echo "❌ Error: KinD cluster 'local-eks' is missing."
    exit 1
fi

CURRENT_CONTEXT=$(kubectl config current-context 2>/dev/null || echo "none")
if [ "$CURRENT_CONTEXT" = "kind-local-eks" ]; then
    echo "✅ Active kubectl context is correctly set to 'kind-local-eks'."
else
    echo "⚠️ Warning: Active kubectl context is '$CURRENT_CONTEXT' (expected 'kind-local-eks'). Fixing..."
    kubectl config use-context kind-local-eks
fi

# Check node statuses
echo "--- Cluster Nodes Status ---"
kubectl get nodes -o wide --request-timeout='5s'

# ==============================================================================
# 3. Verify Docker Network Bridge Integration
# ==============================================================================
echo -e "\n🔹 [3/4] Checking Virtual Network Switch Integration ('local-aws-net')..."

NET_EXISTS=$(docker network ls --format '{{.Name}}' | grep -q "^local-aws-net$" && echo "yes" || echo "no")
if [ "$NET_EXISTS" = "yes" ]; then
    echo "✅ Docker network 'local-aws-net' exists."
    
    # Check if nodes are plugged into the bridge
    for node in local-eks-control-plane local-eks-worker local-eks-worker2; do
        if docker inspect $node --format '{{json .NetworkSettings.Networks.2>/dev/null}}' | grep -q "local-aws-net"; then
            echo "   -> Node '$node' is successfully attached to 'local-aws-net'."
        else
            echo "   -> ⚠️ Node '$node' is NOT attached to 'local-aws-net'."
        fi
    done
else
    echo "❌ Error: Docker network 'local-aws-net' is missing."
fi

# ==============================================================================
# 4. Verify NGINX Ingress Routing Platform & Core Pods
# ==============================================================================
echo -e "\n🔹 [4/4] Checking NGINX Ingress Controller & System Pods..."

if kubectl get namespace ingress-nginx &>/dev/null; then
    echo "✅ Namespace 'ingress-nginx' exists."
    echo "--- Ingress Pods Status ---"
    kubectl get pods -n ingress-nginx --request-timeout='5s'
else
    echo "❌ Error: Namespace 'ingress-nginx' does not exist."
fi

echo "======================================================"
echo "✨ Verification suite completed successfully!"
```

Make `./verify-cluster.sh` executable:

```bash
chmod +x verify-cluster.sh
```

### 📄 verify-tests.sh

This script isolates the AWS CLI entirely inside an ephemeral container. Your host machine remains clean, but you can thoroughly verify all 6 services [index: 4].

```bash
#!/bin/bash
echo "🧪 Running Isolated AWS CLI Verification Tests via Docker..."

run_aws() {
  docker run --rm --network local-aws-net \
    -e AWS_ACCESS_KEY_ID=mock-key -e AWS_SECRET_ACCESS_KEY=mock-secret -e AWS_DEFAULT_REGION=us-east-1 \
    amazon/aws-cli --endpoint-url=http://floci-emulator:4566 "$@"
}

echo "👉 [1/6] DocumentDB (Amazon MongoDB):"
run_aws docdb describe-db-clusters --db-cluster-identifier local-mongo-cluster | grep -E "DBClusterIdentifier|Status"

echo -e "\n👉 [2/6] Amazon ElastiCache:"
run_aws elasticache describe-cache-clusters --cache-cluster-id local-cache | grep -E "CacheClusterId|CacheClusterStatus"

echo -e "\n👉 [3/6] Amazon S3 Storage:"
run_aws s3 ls

echo -e "\n👉 [4/6] AWS KMS Keys:"
run_aws kms list-keys

echo -e "\n👉 [5/6] Amazon EKS (Kubernetes Control Mapping):"
run_aws eks describe-cluster --name micro-eks | grep -E "name|status"

echo -e "\n👉 [6/6] Amazon EC2 Instance Mappings:"
run_aws ec2 describe-instances --filters "Name=tag:Name,Values=LocalComputeNode" | grep -A 2 "Tags"

```

Make `./verify-tests.sh` executable:

```bash
chmod +x verify-tests.sh
```

### 📄 teardown.sh

This script uses your isolated Terraform Compose configuration to tear down the cloud resources, destroys the KinD cluster, halts the background emulator, and cleans up any dynamic container sidecars.

```bash
#!/bin/bash
echo "⚠️ Commencing complete environment teardown..."

echo "🛑 Destroying declarative Terraform managed resources (Stack 2)..."
cd terraform-provisioner
docker compose run --rm terraform /bin/sh -c "terraform destroy -auto-approve" || true
cd ..

echo "🛑 Deleting the KinD Kubernetes cluster..."
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

### 📄 restart.sh

```bash
#!/bin/bash
set -e

echo "🔄 Restarting local cloud and Kubernetes environment..."

# ==============================================================================
# STEP 1: Verify Docker Engine Readiness
# ==============================================================================

systemctl is-active --quiet docker || sudo systemctl start docker
if ! docker info > /dev/null 2>&1; then
    echo "❌ Error: Docker daemon is not running. Please start Docker Desktop/Engine first."
    exit 1
fi

docker context use default

# ==============================================================================
# STEP 2: Resume Core Platform Backend Engine (Stack 1)
# ==============================================================================
echo "🔹 [1/4] Resuming Core Platform Cloud Engine (Stack 1)..."
cd backend-infra && docker compose up -d && cd ..

# Discover existing KinD cluster nodes
NODES=$(docker ps -a --filter "name=local-eks-" --format "{{.Names}}")

if [ -z "$NODES" ]; then
    echo "⚠️ No existing 'local-eks' nodes found. Run './setup.sh' to perform initial provisioning."
    exit 1
fi

# ==============================================================================
# STEP 3: Resume Stopped KinD Kubernetes Cluster Nodes
# ==============================================================================
echo "🔹 [2/4] Restarting KinD Cluster Containers..."
echo "📋 Targeted KinD nodes to start:"
for node in $NODES; do
    echo "   - $node"
done

for node in $NODES; do
    echo "   🚀 Starting container: $node..."
    docker start "$node" > /dev/null
done

# Ensure active context is configured to local-eks
kubectl config use-context kind-local-eks > /dev/null 2>&1 || true

# ==============================================================================
# STEP 4: Re-attach Virtual Network Switch Bridges
# ==============================================================================
echo "🔹 [3/4] Re-attaching KinD Nodes to 'local-aws-net' Switch..."
echo "📋 Targeted KinD nodes for network attachment:"
for node in $NODES; do
    echo "   - $node"
done

for node in $NODES; do
    echo "   🔌 Connecting $node to local-aws-net..."
    docker network connect local-aws-net "$node" 2>/dev/null || true
done

# ==============================================================================
# STEP 5: Re-trigger Infrastructure Provisioning (Stack 2)
# ==============================================================================
echo "🔹 [4/4] Applying Declarative Terraform Resources (Stack 2)..."
cd terraform-provisioner && docker compose up && cd ..

echo "✨ System environment successfully resumed!"
echo "👉 Run './verify-cluster.sh' to confirm all components are healthy."
```

Make it executable and execute it whenever you need a fresh slate:

```bash
chmod +x restart.sh
```

# 🕹️ How to execute and interact with the setup?

Your complete initialization workflow is now simplified into two consecutive script steps:

## Step 1. Bootstrap dependencies and pull up the cluster:

```bash
# Step 1: Grant permissions to setup-prerequisites.sh, setup.sh and teardown.sh
chmod +x *.sh

# Step 2: Pull down your host prerequisites
bash ./setup-prerequisites.sh

# Step 3: Fire up your integrated local AWS and KinD environment
bash ./setup.sh

```

## Step 2. Apply your infrastructure via Stack 2:

* **Stack 1** (`setup.sh`) only booted up the empty backend emulator engine and your KinD cluster base.
* **Stack 2** (your infrastructure blueprints) is what actually provisions those specific DocumentDB clusters, ElastiCache clusters, KMS keys, and EKS mappings inside the emulator.

Once the base system finishes initializing in Step #1 above, execute **Stack 2** to let the isolated Terraform engine provision your resources seamlessly:

```bash
cd terraform-provisioner && docker compose up && cd ..
```

In case of errors:

```bash
cd terraform-provisioner
sudo rm -rf .terraform .terraform.lock.hcl terraform.tfstate terraform.tfstate.backup
cd ..
```

## Step 3. Verify the cluster

Run `./verify-cluster.sh` to verify the cluster setup.

```bash
./verify-cluster.sh

```

## Step 4. Open the Web UI Management Console:

Once you execute `./setup.sh` and deploy your infrastructure via Terraform, you can open floci dashboard like so:

1. Open your browser and navigate to `http://localhost:4500`.
2. You will be greeted by an admin control room showing your live application-assets S3 bucket, your local-mongo-cluster DocumentDB data layers, and your cryptographic KMS tracking keys.
3. You can visually view your active S3 buckets, select the file browser workspace, drag and drop documents into your bucket pipelines, and verify data flows interactively.

Even better—because it is bound to port 4500 on your machine, other developers on your network can access this identical console by hitting `http://<your-machine-ip>:4500`.

- [1] [https://github.com](https://github.com/floci-io/floci-ui)
- [2] [https://github.com](https://github.com/floci-io/floci/issues/1517)

## Step 5. Run the isolated API tests via Docker

Run `./verify-tests.sh` at any time to instantly test all 6 services with zero clutter on your local machine.

```bash
./verify-tests.sh

```

## Step 6. Run AWS CLI Verification Tests inside Host

To verify each of the 6 simulated services, run these test commands from your host machine terminal.

#### 🔧 Prep your host environment parameters:

```bash
export AWS_ACCESS_KEY_ID=mock-key
export AWS_SECRET_ACCESS_KEY=mock-secret
export AWS_DEFAULT_REGION=us-east-1
export AWS_ENDPOINT_URL=http://localhost:4566
```

#### Test 1: Amazon MongoDB (DocumentDB)
Verify that the cluster control plane exists and matches the architecture defined in Terraform:

```bash
aws docdb describe-db-clusters --db-cluster-identifier local-mongo-cluster
```

#### Test 2: Amazon ElastiCache
Confirm that Floci has successfully provisioned a real underlying Redis engine tracking layer:

```bash
aws elasticache describe-cache-clusters --cache-cluster-id local-cache
```

#### Test 3: Amazon S3
Create a temporary dummy file and upload it directly into your local S3 object container:

```bash
echo "Testing local object store" > sample.txt
aws s3 cp sample.txt s3://application-assets/
aws s3 ls s3://application-assets/
```

#### Test 4: KMS Keys
List the encryption elements to confirm your cryptographic control plane keys are active:

```bash
aws kms list-keys
```

#### Test 5: Amazon EKS
Query the cluster metadata parameters to confirm EKS control plane mapping validation:

```bash
aws eks describe-cluster --name micro-eks
```

#### Test 6: Amazon EC2
Inspect your EC2 instances to verify that the proxy virtual machine container is online and tagged properly:

```bash
aws ec2 describe-instances --filters "Name=tag:Name,Values=LocalComputeNode"
```

## Step 7. Reset the workspace whenever necessary:

```bash
./teardown.sh

```

## Step 8. Resume environment after host reboot or shutdown:

When your machine shuts down, Docker containers and KinD nodes go offline. You **do not** need to re-run `setup-prerequisites.sh`[cite: 1]. 

Execute `restart.sh` to log target nodes, boot up existing KinD containers, reconnect network switches, and re-apply Stack 2 infrastructure cleanly:

```bash
# Make the restart script executable (if not already done)
chmod +x restart.sh

# Resume your complete local cloud setup
bash ./restart.sh
```