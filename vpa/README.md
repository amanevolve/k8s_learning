# README

## Vertical Pod Autoscaler (VPA) – Hands-On Guide

---

## 1. Install Metrics Server

```
kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml
kubectl apply -f metrics-server/metrics-server-patch.yml

kubectl top nodes
kubectl top pods -A
```

---

## 2. Install VPA Components

```
kubectl apply -f vpa/vpa-up.yaml
kubectl get pods -n kube-system | grep vpa
kubectl get crd | grep verticalpodautoscalers
```

---

## 3. Create Namespaces

```
kubectl apply -f namespaces/notes-namespace.yml
kubectl apply -f namespaces/load-namespace.yml
```

---

## 4. Deploy Notes App

```
kubectl apply -f notes-app/notes-app.yml
kubectl apply -f notes-app/notes-service.yml
kubectl get pods -n notes-ns
kubectl top pods -n notes-ns
```

---

## 5. Apply VPA

```
kubectl apply -f vpa/vpa.yml
kubectl get vpa -n notes-ns
kubectl describe vpa notes-vpa -n notes-ns
```

---

## 6. Start Load Generator

HTTP load:

```
kubectl apply -f load-generator/load-generator.yml
```

CPU-intensive load:

```
kubectl apply -f load-generator/stress-load.yml
```

---

## 7. Observe VPA Behavior

```
kubectl top pods -n notes-ns
kubectl get vpa -n notes-ns
kubectl describe vpa notes-vpa -n notes-ns
kubectl get pods -n notes-ns -w
```

---

## 8. Verify Updated Pod Resources

```
kubectl describe pod <pod-name> -n notes-ns | grep -A6 -i 'requests\|limits'
```

---

## 9. Cleanup

```
./scripts/delete-all.sh
```

Or manually:

```
kubectl delete ns notes-nns
kubectl delete ns load-ns
```

---


