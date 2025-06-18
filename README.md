# 🧪 Root Container vs Privileged Container Lab

## 🎯 Objective

To show the behavioral and security differences between:

* A Pod running as **root user** (default behavior)
* A Pod running in **privileged mode** (using `securityContext.privileged: true`)

## 🛠️ Test Environment

* Kubernetes Cluster
* kubectl configured
* Test tools: `capsh`, `mount`, `iptables`

## 📋 Test Scenarios

| # | Test                             | Root Pod    | Privileged Pod | Expected              |
| - | -------------------------------- | ----------- | -------------- | --------------------- |
| 1 | Run as UID 0                     | ✅           | ✅              | Both                  |
| 2 | Access to `/dev`                 | ✅ (limited) | ✅              | More in privileged    |
| 3 | Access iptables                  | ❌           | ✅              | Privileged only       |
| 4 | Mount host filesystem            | ❌           | ✅              | Privileged only       |
| 5 | Capabilities                     | ✅ (limited) | ✅ (all)        | More in privileged    |
| 6 | Create a network interface       | ❌           | ✅ (all)        | Privileged only       |

## 📜 Commands to Deploy

```
# Apply root pod
kubectl apply -f root-pod.yaml

# Apply privileged pod
kubectl apply -f privileged-pod.yaml
```

## 🧪 Commands to Run Inside Pods

### Enter root pod:

```
kubectl exec -it root-pod -- bash
```

### Enter privileged pod:

```
kubectl exec -it privileged-pod -- bash
```

## 🔍 Test Commands (Run inside each pod)

```
# 1. Run as UID 0
id

# 2. Access to `/dev`
ls /dev

# 3. Access iptables
iptables -L

# 4. Mount host filesystem
mkdir /mnt/host
lsblk
mount /dev/nvme0n1p1 /mnt/host || echo "mount failed"
ls /mnt/host/

# 5. Capabilities
capsh --print || echo "capsh not available"

# 6. Create a network inerface
ip link add dummy0 type dummy
ip link show dummy0
```

## ✅ Clean Up

```
kubectl delete -f .
```
