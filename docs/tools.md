# Tools

## GitHub
- Manages version control with **`main` (production)** and **`dev` (development)** branches.  
- Pull Requests enforce collaboration, approval, and status checks.  
- Prevents direct commits to **`main`**.  

---

## TeamCity
- Acts as our **Continuous Integration** system.  
- Builds the **Python app**, runs automated unit tests, and reports results.  
- Provides status checks that must pass before merging **PRs**.  

---

## Jenkins
- Specializes in building **container images**.  
- Uses **Jenkinsfile** to automate Docker builds and push images to **DockerHub**.  
- Ensures every build has a versioned tag and a moving latest tag.

---

## DockerHub
- Central registry for all container images.  
- Acts as the trigger source for Spinnaker pipelines.  

---

## Spinnaker (CD)
- Orchestrates **continuous delivery**.  
- Deploys canary first, then promotes to stable if successful.  
- Integrates with DockerHub to auto-trigger on new images.  

---

## Prometheus
- Monitors application health during deployments.
- Tracks latency, error rates, and pod readiness.
- Provides feedback for rollback or promotion decisions.

---

## Istio
- Service mesh that controls **traffic routing**.  
- Splits traffic between **stable** and canary deployments.  
- Provides observability into rollout performance.  

---

## Kubernetes
- Hosts the application workloads.  
- Ensures **five healthy replicas** are always running.  
- Provides scaling, self-healing, and deployment orchestration.
