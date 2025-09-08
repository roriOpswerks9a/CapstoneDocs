# Recovery Plan

The Recovery Plan ensures the system can be restored quickly in case of infrastructure, tool, or configuration failures. Instead of starting from scratch, our approach focuses on backing up and restoring configurations, pipeline definitions, and container images. This allows us to reduce downtime, avoid repetitive manual setup, and guarantee a faster return to operations.

---

## Core Recovery Principles

- **Configuration Backups** – Jenkins pipelines, TeamCity builds, Spinnaker JSON/YAML pipelines, Istio VirtualServices, and Prometheus rules are backed up and versioned.  
- **Immutable Artifacts** – The Python web app is stored in GitHub, while all Docker images (versioned + latest) are pushed to DockerHub. This ensures we can redeploy any version at any time.  
- **Infrastructure Automation** – New VMs or clusters can be provisioned in Google Cloud quickly, then configured with the backed-up definitions.  
- **Separation of Concerns** – CI tools (Jenkins, TeamCity) run in VMs, while CD tools (Spinnaker, Istio, Prometheus) run inside Kubernetes. This separation helps contain recovery scope.  

---

## Recovery Process

If a VM or cluster fails, recovery follows these steps:
<br><br>

#### Reprovision Infrastructure 
- Spin up a new VM or GKE cluster.
<br><br>

#### Restore CI Tools (VM side) 
- Reinstall Jenkins and TeamCity.  
- Import backed-up configurations and credentials.  
<br><br>

#### Restore CD Tools (Cluster side) 
- Reinstall Spinnaker (minimal setup only).  
- Import saved pipeline definitions instead of recreating them.  
- Apply Istio traffic-splitting rules from backups.  
- Reload Prometheus monitoring and alerting configs.  
<br><br>

#### Redeploy Application  
- Pull stable or latest Docker images directly from DockerHub.  
- Roll out to Kubernetes using Spinnaker.  
<br><br>

#### Verify System Health 
- Confirm replicas are healthy in Kubernetes.  
- Validate traffic routing (canary/stable) via Istio.  
- Check monitoring dashboards in Prometheus.  
