## Description of the Solution

Our team has developed a **CI/CD solution** that integrates four key tools across two distinct environments:

- **CI Environment (Google Cloud VM):**  
  Hosts **Jenkins** and **TeamCity** for source integration, build automation, and testing.  
- **CD Environment (Kubernetes Cluster):**  
  Runs **Spinnaker** for deployment orchestration and **Istio** for service mesh traffic management.  

This separation keeps production clean: builds and tests run outside the cluster, while deployments and traffic management stay inside Kubernetes.

The web application is a **Python-based project** (chosen because of its natural compatibility with TeamCity). It is containerized with Docker, versioned through GitHub, and deployed into Kubernetes.

## Deployment Strategy: Canary Release Model

The deployment strategy follows a **canary release model**:

- **Spinnaker** automates the rollout.  
- **Istio** handles traffic routing, initially directing a small percentage of user traffic (e.g., 10%) to the new canary release.  
- **Prometheus** collects real-time performance metrics (latency, error rates, pod health) and provides data for Automated Canary Analysis (ACA).  

If the canary passes health checks → Spinnaker promotes the new version to all users.  

If it fails → Spinnaker triggers an **automatic rollback** to the last stable version.  

This combination ensures safer deployments with reduced risk, high observability, and faster recovery.


---

## Architecture Diagram

```mermaid
flowchart TD
    subgraph VM[Google Cloud VM - CI Environment]
        A[TeamCity] -->|Unit Tests + Build Validation| B[Jenkins]
        B -->|Builds Docker Image| C[DockerHub]
    end

    subgraph K8s[Kubernetes Cluster - CD Environment]
        C --> D[Spinnaker]
        D --> E[Canary Deployment]
        D --> F[Stable Deployment]
        E --> G[Istio Traffic Split]
        F --> G
        G --> H[Users]
        G --> I[Prometheus Metrics]
        I --> D
    end
```

## CI/CD Workflow Diagram

```mermaid
flowchart TD
    A[Developer Commit & Pull Request] --> B[TeamCity Build & Unit Tests]
    B -->|Pass| C[Jenkins Builds Docker Image]
    C --> D[Push Image to DockerHub]
    D --> E[Spinnaker Triggers Deployment]
    E --> F[Canary Pods in Kubernetes]
    F --> G["Istio Splits Traffic<br/><i>10% Canary, 90% Stable</i>"]
    G --> H[Prometheus Monitors Metrics]
    H -->|Healthy| I[Promote Canary to Stable - 100% Traffic]
    H -->|Unhealthy| J[Rollback to Previous Stable]
```
