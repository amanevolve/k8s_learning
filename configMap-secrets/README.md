
---

# README

## MySQL Deployment on Kubernetes (Steps and Commands Only)

---

# 1. Namespace

```
kubectl apply -f namespace.yml
kubectl get ns
```

---

# 2. Secret

Base64 encode password:

```
echo -n 'Admin123' | base64
```

Apply Secret:

```
kubectl apply -f secret.yml
kubectl get secret -n mysql
```

---

# 3. ConfigMap

```
kubectl apply -f configmap.yml
kubectl get configmap -n mysql
```

---

# 4. Deployment

```
kubectl apply -f deployment.yml
kubectl get deploy -n mysql
kubectl get pods -n mysql -o wide
kubectl describe pod <pod-name> -n mysql
```

---

# 5. Service

```
kubectl apply -f service.yml
kubectl get svc -n mysql
```

---

# 6. Verify Pod and Logs

Check pod status:

```
kubectl get pods -n mysql
```

Describe pod:

```
kubectl describe pod <pod-name> -n mysql
```

Check logs:

```
kubectl logs <pod-name> -n mysql
```

Follow live logs:

```
kubectl logs -f <pod-name> -n mysql
```

---

# 7. Exec Into MySQL Container

Get pod name:

```
kubectl get pods -n mysql
```

Enter MySQL shell:

```
kubectl exec -it -n mysql <pod-name> -- mysql -u root -p
```

Password:

```
Admin123
```

---

# 8. Run SQL Commands

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
('Satyam', 'DevOps Trainer', 90000),
('Rohan', 'DevOps Engineer', 80000),
('Rohit', 'DevOps Engineer', 95000);

SELECT * FROM employees;
```

Exit MySQL:

```
EXIT;
```

---

# 9. Verify Environment Variables in Pod

```
kubectl exec -it -n mysql $(kubectl get pod -n mysql -o name) -- env | grep MYSQL
```

Expected:

```
MYSQL_DATABASE=myDB
MYSQL_ROOT_PASSWORD=Admin123
```

---

# 10. Apply All Files

Individually:

```
kubectl apply -f namespace.yml
kubectl apply -f secret.yml
kubectl apply -f configmap.yml
kubectl apply -f deployment.yml
kubectl apply -f service.yml
```

Apply all at once:

```
kubectl apply -f .
```

---

# 11. Cleanup

```
kubectl delete ns mysql
```

---
