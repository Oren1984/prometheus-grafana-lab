# Prometheus & Grafana Installation Guide

This guide explains how to deploy **Prometheus** and **Grafana** on a minimal K3s cluster.  
All steps were tested on Ubuntu 22.04 LTS.

---

## 1. Install K3s (if not already installed)

```bash
curl -sfL https://get.k3s.io | sh -
sudo systemctl enable k3s
sudo systemctl start k3s

Verify cluster:
kubectl get nodes -o wide
kubectl get pods -A


## 2. Add Helm repository
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo add grafana https://grafana.github.io/helm-charts
helm repo update


## 3. Install Prometheus

Namespace:
kubectl create namespace monitoring

Install Prometheus:
helm install prometheus prometheus-community/kube-prometheus-stack -n monitoring

Check:
helm list -n monitoring
kubectl get pods -n monitoring


## 4. Install Grafana (optional, if not included in Prometheus chart)
helm install grafana grafana/grafana -n monitoring
kubectl get pods -n monitoring | grep grafana


## 5. Expose Grafana Service

Edit service type:
kubectl -n monitoring edit svc prometheus-grafana


Change from:
type: ClusterIP


To:
type: NodePort


Check NodePort:
kubectl -n monitoring get svc prometheus-grafana


Access Grafana:
http://<NODE_IP>:<NODE_PORT>


## 6. Get Grafana admin password
kubectl -n monitoring get secret prometheus-grafana -o jsonpath="{.data.admin-password}" | base64 --decode; echo


Default username: admin
Password: (the value above)


## 7. Import Dashboards

Log in to Grafana (http://<NODE_IP>:<NODE_PORT>).

Go to Dashboards → Import.

Paste the JSON model or Grafana dashboard ID.

Click Load.


