
# 🚀 BookInfo CI/CD Pipeline with Istio on Minikube

This repository contains a robust CI/CD pipeline built using **GitHub Actions** to automate the deployment of the **BookInfo application** on **Minikube with Istio service mesh**, integrated with **monitoring tools (Grafana, Prometheus, Kiali, Jaeger, Zipkin)** and **security scanning (Trivy)**.

---

## 📑 Workflow Overview

### Main Features:
- Minikube setup and validation
- Istio installation and configuration
- BookInfo application deployment
- Monitoring stack deployment (Grafana, Prometheus, Kiali, Jaeger, Zipkin)
- Security scanning using Trivy
- Integration and deployment tests
- Email notification
- Final cleanup and deployment summary

---

## 🧭 Pipeline Stages

| Stage | Description |
|-------|-------------|
| **MiniKube_Setup** | Start or resume Minikube cluster |
| **istioctl_install** | Install Istio CLI and perform prechecks |
| **Minikube_IP** | Fetch and display Minikube IP address |
| **Install_Istio** | Install Istio in demo profile and enable injection |
| **Deploy_BookInfo** | Deploy BookInfo application and gateway |
| **check-istio** | Verify Istio setup status |
| **infra-check** | Validate Kubernetes, Minikube, and Istio status |
| **deploy-monitoring** | Deploy monitoring tools in `istio-system` namespace |
| **monitor-[tools]** | Collect dashboard URLs and logs for tools |
| **security-scan** | Perform Trivy scans on BookInfo container images |
| **run-tests** | Port-forward dashboards and verify access logs |
| **deployment-status** | Check app status and gateway URL |
| **notify** | Send email summary and job status |
| **cleanup** | Clean up previous resources |
| **deploy** | Final output with endpoint links |

---

## 📂 Project Structure





# Bookinfo Sample

This repository contains the **Bookinfo** sample application deployed on **Istio** using **Minikube**. The setup includes a full CI/CD pipeline for deploying and monitoring the Bookinfo app with Istio, along with security scanning and infrastructure checks.

## Overview
The **Bookinfo** application consists of multiple microservices demonstrating Istio's capabilities, including traffic routing, observability, and security enforcement. It includes:
- **Productpage** (User-facing service)
- **Details** (Provides book details)
- **Reviews** (Multiple versions exist, one uses ratings)
- **Ratings** (Provides book ratings)

## Prerequisites
Ensure you have the following tools installed before proceeding:
- [Docker](https://www.docker.com/)
- [Minikube](https://minikube.sigs.k8s.io/docs/start/)
- [Kubectl](https://kubernetes.io/docs/tasks/tools/)
- [Istioctl](https://istio.io/latest/docs/setup/install/)
- [Trivy](https://aquasecurity.github.io/trivy/)

## Installation or automated ci/cd pipe line stages 
Follow these steps to deploy Bookinfo with Istio:

1. **Start Minikube**
   ```bash
   minikube start --cpus=4 --memory=8192 --disk-size=50g --kubernetes-version=v1.28.3 --driver=virtualbox
   ```
2. **Install Istio**
   ```bash
   curl -L https://istio.io/downloadIstio | ISTIO_VERSION=1.25.0 sh -
   export PATH=$HOME/.istioctl/bin:$PATH
   istioctl install --set profile=demo -y
   ```
3. **Deploy Bookinfo Application**
   ```bash
   kubectl apply -f bookinfo.yaml
   kubectl apply -f bookinfo-gateway.yaml
   ```
4. **Verify Deployment**
   ```bash
   kubectl rollout status deployment/productpage-v1 -n default --timeout=120s
   ```

## CI/CD Pipeline
The repository includes a **GitHub Actions** CI/CD pipeline that automates:
- Minikube & Istio setup
- Bookinfo app deployment
- Infrastructure verification
- Security scanning with **Trivy**
- Monitoring setup (Grafana, Prometheus, Kiali)
- Cleanup & notifications

### Workflow Structure
#### 1. **Setup & Deployment**
- Minikube starts, and Istio is installed.
- Bookinfo application is deployed.

#### 2. **Monitoring & Security**
- **Grafana, Prometheus, and Kiali** are deployed.
- Security scanning is conducted with Trivy.

#### 3. **Validation & Testing**
- Infrastructure checks are performed.
- Application tests are run.

#### 4. **Notification & Cleanup**
- Status notifications are sent.
- Resources are cleaned up after execution.

## Monitoring
After deploying, check monitoring dashboards:
- **Grafana**: `http://<MINIKUBE_IP>:3000`
- **Prometheus**: `http://<MINIKUBE_IP>:9090`
- **Kiali**: `http://<MINIKUBE_IP>:20001`

## Testing
Once all pods are running, validate the application using CLI or browser.

### Check Running Pods
```bash
kubectl get pods
```
Expected Output:
```bash
NAME                              READY   STATUS    RESTARTS   AGE
details-v1-7f556f5c6b-485l2       2/2     Running   0          10m
productpage-v1-84c8f95c8d-tlml2   2/2     Running   0          10m
ratings-v1-66777f856b-2ls78       2/2     Running   0          10m
reviews-v1-64c47f4f44-rx642       2/2     Running   0          10m
reviews-v2-66b6b95f44-s5nt6       2/2     Running   0          10m
reviews-v3-7f69dd7fd4-zjvc8       2/2     Running   0          10m
```

### Test Using CLI
```bash
kubectl exec -it "$(kubectl get pod -l app=ratings -o jsonpath='{.items[0].metadata.name}')" -c ratings -- curl productpage:9080/productpage | grep -o "<title>.*</title>"
```
Expected Output:
```bash
<title>Simple Bookstore App</title>
```

### Test Using Browser
```bash
http://<MINIKUBE_IP>:31395/productpage
```

## Cleanup
To clean up the deployed resources, run:
```bash
kubectl delete all --all --namespace=default
```

## Useful Commands
| Action | Command |
|--------|---------|
| Get Minikube IP | `minikube ip` |
| List Pods | `kubectl get pods -A` |
| Describe a Pod | `kubectl describe pod <POD_NAME>` |
| Check Istio Precheck | `istioctl x precheck` |
| List Istio Services | `kubectl get svc -n istio-system` |
| Delete Minikube Cluster | `minikube delete` |


