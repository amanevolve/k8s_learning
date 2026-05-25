
# README

## Kubernetes MySQL Deployment with PV, PVC, Deployment & Service

Full Hands-On Lab Guide

---

## 1. Prerequisites

```
kubectl get nodes
kubectl get pods -A
```

---

## 2. Apply Namespace

```
kubectl apply -f namespace.yml
kubectl get ns
```

---

## 3. Create PV & PVC

```
kubectl apply -f pv.yml
kubectl get pv

kubectl apply -f pvc.yml -n mysql
kubectl get pvc -n mysql
kubectl get pv
```

---

## 4. Apply Secret & ConfigMap

```
kubectl apply -f secret.yml
kubectl apply -f configmap.yml

kubectl get secret -n mysql
kubectl get configmap -n mysql
```

---

## 5. Deploy MySQL

```
kubectl apply -f deployment.yml -n mysql
kubectl get pods -n mysql
kubectl describe pod <pod> -n mysql
kubectl logs -n mysql -l app=mysql
```

---

## 6. Create Service

```
kubectl apply -f service.yml -n mysql
kubectl get svc -n mysql
```

---

## 7. Verify Pod, Logs, Debugging

```
kubectl get pods -n mysql
kubectl describe pod <pod> -n mysql
kubectl logs <pod> -n mysql
kubectl logs -f <pod> -n mysql
```

---

## 8. Exec Into Pod and Run SQL Commands

```
kubectl get pods -n mysql
kubectl exec -it -n mysql <pod> -- mysql -u root -p
```

Password: `Admin123`

Inside MySQL:

```
SHOW DATABASES;
USE myDB;

CREATE TABLE employees (
  id INT AUTO_INCREMENT PRIMARY KEY,
  name VARCHAR(50),
  role VARCHAR(50),
  salary INT
);

INSERT INTO employees (name, role, salary)
VALUES
('Satyam','DevOps Trainer',90000),
('Rohan','DevOps Engineer',80000),
('Rohit','DevOps Engineer',95000);

SELECT * FROM employees;
EXIT;
```

---

## 9. Verify Environment Variables

```
kubectl exec -it -n mysql $(kubectl get pod -n mysql -o name) -- env | grep MYSQL
```

Expected:

```
MYSQL_DATABASE=myDB
MYSQL_ROOT_PASSWORD=Admin123
```

---

## 10. Persistence Test (Delete Pod)

```
kubectl delete pod -n mysql --all
kubectl get pods -n mysql -w
```

Reconnect and verify:

```
kubectl exec -it -n mysql <pod> -- mysql -u root -p
USE myDB;
SELECT * FROM employees;
```

Data must still be present.

---

## 11. PV Data Check on Node (hostPath only)

Find node:

```
kubectl get pods -n mysql -o wide
```

Enter node:

```
docker exec -it <node-name> bash
ls -l /mnt/data/mysql
```

---

## 12. Cleanup

```
./delete-all.sh
```

OR manually:

```
kubectl delete ns mysql
sudo rm -rf /mnt/data/mysql
```

---


