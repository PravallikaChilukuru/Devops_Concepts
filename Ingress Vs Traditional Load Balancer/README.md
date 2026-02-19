# Kubernetes: Ingress vs. LoadBalancer

A quick guide to understanding how we expose applications to the external world in K8s.

---

## 1. The Comparison Table

| Feature | LoadBalancer Service | Ingress (The Modern Way) |
| :--- | :--- | :--- |
| **OSI Layer** | Layer 4 (Network/TCP) | Layer 7 (Application/HTTP) |
| **Cost** | **High**: One LB per Service. | **Low**: One LB for many Services. |
| **Traffic Split** | Based on IP and Port. | Based on URL Path or Domain Name. |
| **Analogy** | A separate front door for every room. | One main lobby with a receptionist. |

---

## 2. How the Ingress Controller Decides
The **Ingress Controller** acts as the "brain." It distributes traffic based on two main factors:

### A. Routing Rules (Where to go?)
* **Host-based:** `api.myapp.com` -> Service A | `web.myapp.com` -> Service B.
* **Path-based:** `myapp.com/images` -> Service A | `myapp.com/login` -> Service B.

### B. Distribution Algorithms (Which Pod?)
* **Round Robin:** Sends traffic to Pods in a 1-2-3 sequence (Standard).
* **Least Connections:** Sends traffic to the Pod that is the least busy.
* **IP Hashing:** Keeps a specific user connected to the same Pod (Sticky Sessions).

---

## 3. The Architecture

1.  **Traffic** hits a single External IP.
2.  **Ingress Controller** reads the HTTP Header (URL).
3.  **Rules** match the URL to a specific **Service**.
4.  **Algorithm** picks a healthy **Pod** to handle the request.

---

## 4. Why use Ingress in Enterprise?
* **SSL Termination:** Handle HTTPS certificates in one place instead of in every app.
* **Namespace support:** Route traffic to different services across the whole cluster.
* **Efficiency:** Save money by using a single Cloud Load Balancer.

---
# 🚦 Kubernetes Ingress Traffic Distribution Explained

An **Ingress Controller** distributes traffic based on rules defined in your YAML configuration, and it uses algorithms to decide which specific Pod gets the request.

---

## 1️⃣ The Rules (The "Where")

The Ingress Controller first examines the **HTTP request header** and matches it against the rules defined in your Ingress YAML.

### 🔹 Host-Based Routing (Domain-Based)

Routes traffic based on the domain name.

Example:

api.example.com   → Service A  
web.example.com   → Service B  

---

### 🔹 Path-Based Routing (URL-Based)

Routes traffic based on the URL path.

Example:

example.com/orders    → Service A  
example.com/products  → Service B  

---

## 2️⃣ The Distribution Logic (The "How")

Once the Ingress Controller determines the target **Service**, it must select a specific **Pod**.  
It follows defined load-balancing algorithms.

| Method | How It Works | When To Use It |
|--------|--------------|---------------|
| **Round Robin** | Sends requests to Pods in circular order (1 → 2 → 3 → 1). | When all Pods have equal CPU/RAM capacity. |
| **Least Connections** | Sends traffic to the Pod handling the fewest active requests. | For long-running tasks (e.g., file uploads). |
| **IP Hashing** | Uses client IP to consistently route a user to the same Pod. | For Sticky Sessions (session persistence). |
| **Random** | Selects a healthy Pod randomly. | Large clusters where traffic naturally balances. |

---

## 3️⃣ Health Checks (The "Who is Alive?")

Ingress Controllers rely on **Readiness Probes**.

- If a Pod is **crashing** or still **loading**, it is marked as *unhealthy*.
- The Controller **removes it from rotation** immediately.
- This prevents users from seeing `404` or `500` errors.

---

## 4️⃣ Advanced: Metrics-Based Scaling

The Ingress Controller works closely with the **Horizontal Pod Autoscaler (HPA)**.

### How it works:

1. Traffic spikes (high Requests Per Second).
2. Kubernetes detects increased CPU/memory usage.
3. HPA automatically spins up more Pods.
4. The Ingress Controller detects new Pods.
5. Traffic is automatically distributed to them.

---

## 🧠 In Short

- **Rules (Host/Path)** → Decide *where* traffic should go.
- **Algorithm (Round Robin, etc.)** → Decide *which Pod* handles the request.
- **Health Checks** → Ensure only healthy Pods receive traffic.
- **HPA** → Automatically scales Pods during traffic spikes.
