Pre-requisite: Read [AWS Setup Guide](../setup-aws-with-floci-terraform/README.md)

# 🔄 Session Persistence: Keeping Your State Between Restarts

# Q. The **AWS Setup Guide** provides a repeatable way to spin up local AWS simulation environment. How can I persist state across restarts and allow remote developer access to my local AWS simulation?

No, you do not have to rerun setup.sh every time. Running setup.sh is a one-time onboarding action.
Because we mounted your local host directories and assigned explicit container names, both your Terraform state metrics and your KinD cluster storage are naturally preserved on your hard drive.

# Q. Do I have to run setup.sh every single time? Is there a way to set it up once, stop/start the environment as needed, and have it automatically restore all my created resources from where I left off?

To stop your work at the end of the day and resume exactly where you left off without destroying or recreating anything, use standard Docker and KinD lifecycle commands:

## 🛑 To Pause/Stop Everything:

### Step 1. Stop the core Floci AWS emulator container gracefully

```bash
cd backend-infra && docker compose stop && cd ..
```

### Step 2. Stop the KinD cluster node containers directly

```bash
docker stop local-eks-control-plane local-eks-worker local-eks-worker2
```

## 🚀 To Resume/Start Everything Back Up:

### Step 1. Wake up the Floci AWS engine

```bash
cd backend-infra && docker compose start && cd ..
```

### Step 2. Wake up the high-fidelity Kubernetes node cluster matrix

```bash
docker start local-eks-control-plane local-eks-worker local-eks-worker2
```

Note: Your S3 buckets, KMS keys, database endpoints, and active Kubernetes pods will all be fully restored and accessible within seconds. [1] 

# 🌐 Cross-Team Collaboration: Sharing Your AWS Simulator

# Q. Can other devs connect to the simulation running on my local machine instead of real AWS? If yes, what are the steps to set that up?

Yes, other developers on your local area network (LAN) or VPN can connect to your machine and interact with your Floci emulator as if they were targeting a real AWS cloud region.
Floci acts as a centralized cloud portal endpoint on your workstation. Because it bypasses real AWS signature validations (SigV4), your peers do not need your local access keys—they just need access to your network port.

## Step 1: Find Your Local IP Address
On your host machine, locate your active internal network IP address (e.g., 192.168.1.150 or your corporate WireGuard VPN IP address):

* macOS / Linux: ipconfig getifaddr en0 or hostname -I
* Windows (PowerShell): Get-NetIPAddress -InterfaceAlias Wi-Fi | Select-Object IPAddress [2] 

## Step 2: Ensure Port 4566 is Exposed
Your backend-infra/docker-compose.yml is already configured to bind 4566:4566. This means it listens globally on all interfaces (0.0.0.0:4566). Ensure your local OS firewall or router is not blocking inbound TCP traffic on port 4566.

## Step 3: Steps for the Other Developers to Connect
Other engineers on your team can instantly run tests, view assets, or upload files directly to your machine by updating their local shell context configurations:

# 1. They configure dummy keys (Floci accepts any string)

```bash
export AWS_ACCESS_KEY_ID=peer-developer-key
export AWS_SECRET_ACCESS_KEY=peer-developer-secret
export AWS_DEFAULT_REGION=us-east-1
```

# 2. THEY POINT THEIR ENDPOINT TO YOUR MACHINE'S IP ADDRESS 🚀

```bash
export AWS_ENDPOINT_URL=http://192.168.1.150:4566
```

# 3. They can now immediately view or upload to your local simulated S3 bucket!

```bash
aws s3 ls
aws s3 cp patch.zip s3://application-assets/
```

If they are writing application code (Node.js, Go, Python, Java) on their own computers, they simply need to configure their code's AWS SDK client initialization block to target your IP endpoint address: http://192.168.1.150:4566.
------------------------------
Would you like help creating a quick bash alias profile snippet you can share with your team so they can toggle between real AWS and your local machine with a single command?

[1] [https://neon.com](https://neon.com/blog/announcing-point-in-time-restore)
[2] [https://www.linkedin.com](https://www.linkedin.com/pulse/3-nodes-zero-code-persistent-memory-n8n-workflows-vectorizeio-0vfpe)
