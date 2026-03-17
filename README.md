# Kubernetes eBPF Networking & Security Lab 🚀

Welcome! This repository contains a production-ready networking lab focused on advanced Kubernetes networking using **Cilium CNI** and **eBPF**. 

As a Network Engineer transitioning into DevOps, my goal was to demonstrate how modern container networking bypasses traditional `iptables` bottlenecks to provide high-performance, secure, and observable traffic flow.

---

## 🛠 Project Highlights

- **Cluster Topology:** Multi-node cluster built with `Kind` (Kubernetes in Docker).
- **Next-Gen CNI:** **Cilium** installed in `kube-proxy-free` mode using **eBPF** for all service routing and load balancing.
- **Deep Observability:** **Hubble** enabled for real-time traffic visualization and flow logging.
- **L7 Network Security:** Advanced **Zero-Trust** policies implemented at the HTTP layer (not just IP/Port filtering).
- **Network Debugging:** Integrated **netshoot** pods for professional connectivity analysis (curl, dig, hping3).

---

## 🏗 Lab Architecture

This lab runs on a single Linux VM using Docker. It simulates a real-world multi-node environment with:
- `1x Control Plane`
- `2x Worker Nodes`

The communication between nodes is handled by Cilium using **eBPF maps** instead of the legacy `kube-proxy` (iptables/ipvs), significantly reducing latency and CPU overhead.

---

## 🔒 Security Showcase: Layer 7 Network Policies

The core of this project is the **L7 Policy Lab**. Most standard K8s setups use `NetworkPolicies` that only filter by IP or Port (Layer 3/4). 

In this lab, I've implemented a **CiliumNetworkPolicy** that inspects HTTP headers:
- **Allow:** `GET /v1/public`
- **Block:** Everything else (e.g., `POST`, other paths)

### Proof of Concept (Logs from my lab)

Before applying the policy, all traffic was allowed. After applying `l7-policy.yaml`:

```bash
# Testing GET /v1/public (ALLOWED)
$ kubectl exec netshoot -n lab -- curl -s -XGET http://deathstar/v1/public
{"code":404,"message":"path /v1/public was not found"} # Response received!

# Testing POST /v1/request-landing (BLOCKED by Cilium)
$ kubectl exec netshoot -n lab -- curl -s -XPOST http://deathstar/v1/request-landing
Access denied # Blocked at the kernel level by eBPF!
```

---

## 🚀 How to Run

1. **Prerequisites:** Docker, Kind, Kubectl, Helm.
2. **Create Cluster:**
   ```bash
   kind create cluster --config kind-config.yaml --name networking-lab
   ```
3. **Install Cilium:**
   ```bash
   helm repo add cilium https://helm.cilium.io/
   helm install cilium cilium/cilium --version 1.15.1 --namespace kube-system --values cilium-values.yaml
   ```
4. **Deploy Lab:**
   ```bash
   kubectl apply -f app-test.yaml
   kubectl apply -f l7-policy.yaml
   ```

---

## 👨‍💻 About Me

I am a **Network Engineer** with a passion for Infrastructure-as-Code and Cloud-Native networking. I specialize in bridging the gap between traditional networking (BGP, OSPF, VLANs) and modern container orchestration.

- **LinkedIn:**  www.linkedin.com/in/mateuszwozniak50/

---

*This project was built to demonstrate proficiency in Kubernetes CNI, eBPF, and modern security practices.*
