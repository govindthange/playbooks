# Q. How to set up Local Prometheus and Grafana dashboards inside KinD to monitor your mock cloud resource metrics?

To monitor your applications and mock cloud resource metrics natively inside your high-fidelity KinD cluster, the industry standard is to deploy the kube-prometheus-stack.

This packages Prometheus, Grafana, and the required node-exporters into a unified deployment. [1, 2, 3, 4, 5]

To maintain our core design requirement of "Zero-Host-Pollution", we will not install Helm natively on your machine. Instead, we will extend your automated orchestration script (setup.sh) to download and run Helm inside an ephemeral, self-cleaning Docker container, applying the monitoring tools straight into KinD.

## 📂 Updated Folder Layout

We will create a new directory called local-monitoring/ to house your Grafana configuration dashboards:

```bash
local-cloud-env/
├── backend-infra/
│   └── docker-compose.yml
├── local-kubernetes/
│   └── kind-config.yaml
├── local-monitoring/
│   └── grafana-ingress.yaml     # 🆕 Direct routing rule for your web dashboards
├── terraform-provisioner/
│   ├── docker-compose.yml
│   ├── provider.tf
│   └── main.tf
├── setup-prerequisites.sh
├── setup.sh                     # 🔄 Updated to auto-inject Prometheus & Grafana
├── verify-tests.sh
└── teardown.sh
```

## 💾 1. The Ingress Configuration File

## 📄 local-monitoring/grafana-ingress.yaml

This file maps an easy-to-remember domain name using our wildcard network rule directly to the internal Grafana dashboard service.

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: grafana-ingress
  namespace: monitoring
spec:
  ingressClassName: nginx
  rules:
  - host: grafana.127.0.0.1.sslip.io
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: prometheus-grafana
            port:
              number: 80

```

## ⚙️ 2. The Updated Master Automation Setup Script## 📄 setup.sh
We are appending a dedicated Step 5 to the script. This steps mounts your native Kubernetes security context (~/.kube) into a standard alpine-helm container to run the deployment routines securely.

```bash
#!/bin/bash
set -e

echo "🚀 Booting Core Infrastructure Layer (Stack 1)..."
cd backend-infra && docker compose up -d && cd ..

echo "🚀 Constructing Multi-Node High-Fidelity KinD Cluster..."
kind create cluster --name local-eks --config local-kubernetes/kind-config.yaml

echo "🚀 Binding Containerized Cluster Routing Targets to AWS Network..."
docker network connect local-aws-net local-eks-control-plane
docker network connect local-aws-net local-eks-worker
docker network connect local-aws-net local-eks-worker2

echo "🚀 Rollout NGINX Edge Ingress Platforms inside KinD..."
kubectl apply -f https://githubusercontent.com
kubectl wait --namespace ingress-nginx --for=condition=ready pod --selector=app.kubernetes.io/component=controller --timeout=90s

# =========================================================================
# 🆕 STEP 5: CONTAINERIZED PROMETHEUS & GRAFANA DEPLOYMENT
# =========================================================================
echo "🚀 Deploying Local Prometheus & Grafana via Isolated Helm Container..."

# Helper tracking function to run Helm inside a container without host pollution
run_helm() {
  docker run --rm \
    --network host \
    -v "$HOME/.kube:/root/.kube" \
    alpine/helm:3.12.0 "$@"
}

echo "🔹 Creating monitoring namespace namespace boundaries..."
kubectl create namespace monitoring --dry-run=client -o yaml | kubectl apply -f -

echo "🔹 Synchronizing Prometheus Community Helm chart repositories..."
run_helm repo add prometheus-community https://github.io
run_helm repo update

echo "🔹 Launching the Kube-Prometheus stack engine..."
# Disables default persistent volume demands to ensure it runs completely in ephemeral RAM
run_helm upgrade --install prometheus prometheus-community/kube-prometheus-stack \
  --namespace monitoring \
  --set prometheus.prometheusSpec.storageSpec.emptyDir.medium=Memory \
  --set alertmanager.alertmanagerSpec.storageSpec.emptyDir.medium=Memory \
  --set grafana.adminPassword=admin

echo "🔹 Configuring local domain routing ingress rules rules for Grafana dashboards..."
kubectl apply -f local-monitoring/grafana-ingress.yaml

echo "⏳ Waiting for Grafana UI engines to complete boot cycles..."
kubectl wait --namespace monitoring \
  --for=condition=ready pod \
  --selector=app.kubernetes.io/name=grafana \
  --timeout=120s

echo "✨ Base Grid Setup online! Launch Stack 2 next to apply Terraform profiles."

```

## 🕹️ How to Access and View Metrics

1. Execute your updated environment generation script:

```bash
./setup.sh
```

[6] 
2. Deploy your cloud infrastructure blueprint via Terraform (Stack 2) as normal:

```bash
cd terraform-provisioner && docker compose up && cd ..
```

[7] 

3. Open the Grafana Metrics Dashboard:

Open your browser and navigate directly to: http://sslip.io

* Username: admin
* Password: admin (as hardcoded in our helm values rule override above) [8, 9] 
   
## 📊 Pre-Loaded Dashboard Views to Explore:

Once logged in, click the Dashboards menu icon on the left panel hierarchy. The stack automatically comes pre-packaged with complete production dashboards tracking your simulated cluster architecture: [10, 11] 

* Select "Kubernetes / Compute Resources / Cluster" to see live, real-time memory and CPU utilization graphs for your control-plane and worker containers. [12] 
* Select "Kubernetes / Networking / Ingress" to see live visual metric representations of every single curl call hitting your applications via the NGINX router.

## 🌐 Sharing Metrics across your Team:

Just like our application endpoints, your team members can bypass host configurations completely. If your workstation internal LAN IP address is 192.168.1.150 [index: 4], they can view your live metrics panel dashboards on their own screens by typing http://sslip.io directly into their browsers.
Would you like to examine how to instrument your Node.js/Python microservices to push custom application business metrics (like tracking transaction speed or payment volumes) straight into this Prometheus collector framework?

- [1] [https://www.askantech.com](https://www.askantech.com/observability-engineering-metrics-logs-traces-monitoring-stack/)
- [2] [https://upcloud.com](https://upcloud.com/resources/tutorials/monitoring-upcloud-prometheus-part-3/)
- [3] [https://www.linkedin.com](https://www.linkedin.com/pulse/prometheus-monitoring-setup-practical-guide-engineering-teams-xfaof)
- [4] [https://spacelift.io](https://spacelift.io/blog/prometheus-kubernetes)
- [5] [https://levelup.gitconnected.com](https://levelup.gitconnected.com/serverless-gpus-keda-scale-to-zero-llama-cpp-and-observability-5b58b70af252)
- [6] [https://grafana.com](https://grafana.com/blog/how-to-set-up-a-grafana-development-environment-on-a-windows-pc-using-wsl/)
- [7] [https://medium.com](https://medium.com/@tashuanlawrence7/building-a-production-grade-site-reliability-engineering-sre-monitoring-stack-on-aws-with-e27741527399)
- [8] [https://github.com](https://github.com/isovalent/cilium-grafana-observability-demo)
- [9] [https://nirmata.com](https://nirmata.com/2021/06/18/monitoring-kyverno-with-prometheus/)
- [10] [https://www.apptio.com](https://www.apptio.com/topics/kubernetes/monitoring/grafana-dashboard/)
- [11] [https://medium.com](https://medium.com/@neamulkabiremon/production-ready-observability-with-prometheus-loki-grafana-2ce1ba9f7423)
- [12] [https://medium.com](https://medium.com/@abhithind31/setting-up-prometheus-and-grafana-in-kubernetes-cluster-using-helm-charts-and-configuring-email-cba1a28071d5)
