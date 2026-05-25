

# Vertical Pod Autoscaler (VPA) – Quick Install Guide

This guide explains how to quickly deploy the **Vertical Pod Autoscaler (VPA)** in your Kubernetes cluster using the official Kubernetes Autoscaler repository.

---

##  1. Clone the Repository

VPA installation scripts are included in the Kubernetes Autoscaler repo.
To clone and navigate into the VPA directory:

```bash
git clone https://github.com/kubernetes/autoscaler.git
cd autoscaler/vertical-pod-autoscaler
```

---

##  2. Deploy VPA Components (Recommended: Auto-Script)

Inside the VPA directory, run the install script:

```bash
./hack/vpa-up.sh
```

This script automatically creates the required namespace and deploys the three core VPA components:

* **Recommender**
* **Updater**
* **Admission Controller**

---

##  3. Verify Deployment

Check if the VPA pods are running:

```bash
kubectl get pods -n kube-system | grep vpa
```

Or check across all namespaces:

```bash
kubectl get pods -A | grep vpa
```

You should see the **recommender**, **updater**, and **admission-controller** running.

---

