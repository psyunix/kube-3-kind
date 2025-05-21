# kube-3-kind

## How to Spin Up 3 Kubernetes Clusters with Docker Desktop

Docker Desktop provides a built-in Kubernetes environment but supports only a single-cluster setup natively. To run multiple Kubernetes clusters, you need to use Kind (Kubernetes IN Docker) or K3d (K3s in Docker).

This tutorial will show you how to spin up three Kubernetes clusters using Kind, which is an easy-to-use tool for running Kubernetes in Docker containers.

---

## Prerequisites

Before we begin, ensure you have the following installed:

- **Docker Desktop** (with Kubernetes enabled)  
  👉 [Install Docker](https://docs.docker.com/get-docker/)
- **kubectl** (Kubernetes CLI)  
  👉 [Install kubectl](https://kubernetes.io/docs/tasks/tools/)
- **Kind** (Kubernetes IN Docker)  
  👉 Install it using:

    ```bash
    curl -Lo ./kind https://kind.sigs.k8s.io/dl/latest/kind-linux-amd64
    chmod +x ./kind
    sudo mv ./kind /usr/local/bin/kind
    ```

    For macOS users, install via Homebrew:

    ```bash
    brew install kind
    ```

---

## Step 1: Create Kind Cluster Configurations

We will define three clusters: `cluster1`, `cluster2`, and `cluster3`.

Create a configuration file for each cluster:

**Cluster 1 - cluster1.yaml**
```yaml
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
name: cluster1
nodes:
  - role: control-plane
  - role: worker
  - role: worker
networking:
  podSubnet: "10.244.0.0/16"
  serviceSubnet: "10.96.0.0/16"
```

**Cluster 2 - cluster2.yaml**
```yaml
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
name: cluster2
nodes:
  - role: control-plane
  - role: worker
  - role: worker
networking:
  podSubnet: "10.245.0.0/16"
  serviceSubnet: "10.97.0.0/16"
```

**Cluster 3 - cluster3.yaml**
```yaml
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
name: cluster3
nodes:
  - role: control-plane
  - role: worker
  - role: worker
networking:
  podSubnet: "10.246.0.0/16"
  serviceSubnet: "10.98.0.0/16"
```

---

## Step 2: Create the Clusters

Run the following commands in your terminal:

```bash
kind create cluster --config cluster1.yaml
kind create cluster --config cluster2.yaml
kind create cluster --config cluster3.yaml
```

This will create three separate Kubernetes clusters inside Docker containers.

You can verify the running clusters using:

```bash
kind get clusters
```

---

## Step 3: Configure kubectl to Switch Between Clusters

Since we have multiple clusters, we need to switch contexts when managing them.

1. Get the current kubeconfig settings:
    ```bash
    kubectl config get-contexts
    ```
2. Switch to cluster1:
    ```bash
    kubectl config use-context kind-cluster1
    ```
3. Check nodes in cluster1:
    ```bash
    kubectl get nodes
    ```
4. Switch to cluster2:
    ```bash
    kubectl config use-context kind-cluster2
    ```
5. Check nodes in cluster2:
    ```bash
    kubectl get nodes
    ```
6. Similarly, switch to cluster3:
    ```bash
    kubectl config use-context kind-cluster3
    ```

---

## Step 4: Deploy a Sample App in Each Cluster

Now, let’s deploy a simple Nginx web server on each cluster.

1. Create a deployment manifest `nginx-deployment.yaml`:

    ```yaml
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: nginx
    spec:
      replicas: 2
      selector:
        matchLabels:
          app: nginx
      template:
        metadata:
          labels:
            app: nginx
        spec:
          containers:
            - name: nginx
              image: nginx:latest
              ports:
                - containerPort: 80
    ```

2. Apply this deployment on each cluster:

    ```bash
    kubectl --context kind-cluster1 apply -f nginx-deployment.yaml
    kubectl --context kind-cluster2 apply -f nginx-deployment.yaml
    kubectl --context kind-cluster3 apply -f nginx-deployment.yaml
    ```

3. Check the running pods:

    ```bash
    kubectl --context kind-cluster1 get pods
    kubectl --context kind-cluster2 get pods
    kubectl --context kind-cluster3 get pods
    ```

---

## Step 5: Expose the Application

We need to expose the Nginx deployment using a Service.

1. Create a `service.yaml` file:

    ```yaml
    apiVersion: v1
    kind: Service
    metadata:
      name: nginx-service
    spec:
      selector:
        app: nginx
      ports:
        - protocol: TCP
          port: 80
          targetPort: 80
      type: LoadBalancer
    ```

2. Apply the service on all three clusters:

    ```bash
    kubectl --context kind-cluster1 apply -f service.yaml
    kubectl --context kind-cluster2 apply -f service.yaml
    kubectl --context kind-cluster3 apply -f service.yaml
    ```

3. Get the service details:

    ```bash
    kubectl --context kind-cluster1 get svc
    kubectl --context kind-cluster2 get svc
    kubectl --context kind-cluster3 get svc
    ```

---

## Step 6: Clean Up

When you are done, delete the clusters to free up resources.

```bash
kind delete cluster --name cluster1
kind delete cluster --name cluster2
kind delete cluster --name cluster3
```

---

## Conclusion

You have successfully created three separate Kubernetes clusters using Kind on Docker Desktop. You deployed Nginx and exposed it via a service in each cluster. This setup is useful for testing multi-cluster deployments, service meshes (like Istio or Linkerd), and Kubernetes federation.


